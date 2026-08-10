# Configuration Firebase

L'application (`index.html`) a besoin d'un projet Firebase pour l'authentification et le partage des données en temps réel.

## 1. Créer le projet

1. Va sur https://console.firebase.google.com et crée un projet (gratuit, offre "Spark").
2. Dans **Compilation > Authentication**, active la méthode de connexion **E-mail/Mot de passe**.
3. Dans **Compilation > Firestore Database**, crée une base en **mode production**.
4. Dans **Paramètres du projet > Général > Tes applications**, ajoute une application **Web** et copie l'objet `firebaseConfig` (apiKey, authDomain, projectId, etc. — ce ne sont pas des secrets, ils peuvent être publics dans le code).
5. Colle ces valeurs dans `index.html`, à la place des `"REMPLACER..."` (tout en haut du `<script>`, juste après les balises Firebase).

## 2. Publier les règles de sécurité

Dans **Firestore Database > Règles**, colle le contenu du fichier `firestore.rules` de ce dépôt, puis publie.

Ces règles garantissent que :
- seuls les comptes connectés peuvent lire les données ;
- seuls les comptes avec le rôle `editor` peuvent écrire (créer/modifier/supprimer un palox, changer la config des cellules) ;
- personne ne peut modifier son propre rôle depuis l'application (uniquement via la console Firebase).

## 3. Créer les comptes utilisateurs

1. Dans **Authentication > Users**, clique sur **Ajouter un utilisateur** pour chaque personne (email + mot de passe temporaire — elle pourra le changer plus tard via "mot de passe oublié" si tu actives cette option).
2. Note l'**UID** généré pour chaque personne.
3. Dans **Firestore Database > Données**, crée une collection `users`. Pour chaque personne, crée un document dont l'**ID du document est son UID**, avec un champ :
   - `role` (string) = `"editor"` pour la gestion (lecture/écriture)
   - `role` (string) = `"viewer"` pour la consultation seule

Sans document dans `users`, un compte connecté est traité comme `viewer` par défaut.

## 4. Domaines autorisés

Dans **Authentication > Settings > Authorized domains**, ajoute le domaine où `index.html` sera hébergé (ex : `tonorg.github.io` si tu utilises GitHub Pages).

## 5. Tester

Ouvre `index.html` dans un navigateur, connecte-toi avec un compte `editor` : tu dois voir l'onglet "Cellules" et pouvoir ajouter/sortir/déplacer des palox. Avec un compte `viewer`, ces actions doivent être masquées et l'onglet "Cellules" absent.
