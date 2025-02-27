# Write-ups MINI-CTF
## Challenge WEB
### [Easy] Surfer
En nous rendant sur l'IP fourni, nous arrivons sur une page de login.
Rien n'est présent dans les commentaires de cette page.
Si on vérifie les fichiers présents généralement sur les pages web, comme par exemple : <ins>robots.txt</ins>
On se rend compte qu'il y a cette entrée : 
> User-Agent: *
> Disallow: /backup/chat.txt

En naviguant sur la page Disallow, on se retrouve avec une conversation entre l’utilisateur Kate et Admin.
> Admin: I have finished setting up the new export2pdf tool.
> Kate: Thanks, we will require daily system reports in pdf format.
> Admin: Yes, I am updated about that.
> Kate: Have you finished adding the internal server.
> Admin: Yes, it should be serving flag from now.
> Kate: Also Don't forget to change the creds, plz stop using your username as password.
> Kate: Hello.. ?

On obtient deux informations important avec cet échange.
1 - L'utilisateur Admin utilise son nom d'utilisateur en mot de passe
2 - Un outil nommé export2pdf permet d'extraire des informations

On peut donc retourner sur la page de login et s'authentifier en utilisant *admin/admin*.
Une fois authentifié, on se retrouve avec un dashboard administrateur.

Sur ce dashboard se retrouve le fameux outil d'extraction en PDF.
Quand on l'utilise normalement, il nous génère un rapport du système.
Dans ce rapport, une information utile apparait "*Report generated for http://127.0.0.1/server-info.php*".
Les rapports sont donc générés depuis l'adresse LocalHost du serveur.

Toujours sur la Dashboard, on observe dans la colonne événement récente à gauche cette information : 
> Internal pages hosted at /internal/admin.php. It contains the system flag. 

Quand on se rend sur la page, le message suivant apparait : 
> This page can only be accessed locally.

Quand on inspecte le code du bouton permettant l'extraction en PDF
```
<form class="row g-3 needs-validation" novalidate action="export2pdf.php" method="POST">
  <input type="hidden" id="url" name="url" value="http://127.0.0.1/server-info.php">
  <div class="col-12">
  <button class="btn btn-primary w-100" type="submit">Export to PDF</button>
  </div>
</form>
```
On se rend compte qu'un champ URL est caché.
Si on modifie le fichier que va chercher la fonction export2pdf.php, 
cette à-dire *http://127.0.0.1/server-info.php* **=>** *http://127.0.0.1/internal/admin.php
Le fichier extrait en PDF est bien la page */internal/admin.php*
