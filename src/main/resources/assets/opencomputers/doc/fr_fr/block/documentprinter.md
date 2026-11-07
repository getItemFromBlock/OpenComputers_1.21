# Imprimante de documents

![Encre non fournie.](item:opencomputers:document_printer)

L'imprimante de documents est le périphérique d'OpenPrinter intégré dans OpenComputers. Connectez-là à un réseau OPenComputers et accédez-y en utilisant le périphérique `openprinter` en lua.

Chargez de l'encre en utilisant une [cartouche d'encre noire ou de couleur](../item/printerink.md) dans les slots à gauche et du papier dans l'entrée à papier. Le slot `scanner` accepte des [pages imprimées](../item/printedpage.md) et des livres Minecraft vanilla. Une fois imprimées, les pages apparaissent dans les slots du bas.

L'imprimante utilise une queue FIFO (first in, first out - premier arrivé, premier sorti). Les impressions se mettent en pause automatiquement s'il n'y a plus de papier, d'encre ou d'énergie et elles reprennent dès que le problème est résolu. Les pages imprimées sont placées en prioritées dans le premier dossier avec de la place disponible de la sortie plutot que dans un slot vide. Il est possible de mettre n'importe quelle quantité de dossiers dans la sortie, et les slots d'output vide seront ensuite utilisésd quand tous les dossiers sont remplis.

## API moderne

La queue de l'API créée des documents et retourne un ID d'impression :

```lua
local printer = require("component").openprinter

local job, reason = printer.print("Line 1\nLine 2", {
  title = "Report",
  copies = 2,
  wrap = true
})
assert(job, reason)
```

Les documents formatés peuvent utiliser une couleur différente par ligne ainsi que d'alignement le texte :

```lua
printer.print({
  title = "Status",
  lines = {
    {text = "OpenComputers", color = 0x3366FF, alignment = "center"},
    {text = "Ready", color = 0x000000, alignment = "left"}
  }
})
```

Voici une liste de certains fonctions utiles :

* `print(document[, options])` — ajoute un document à la queue.
* `printLabel(text[, options])` — imprime des étiquettes.
* `printMap(image[, options])` — imrpime une table de pixels RGB sur une carte vierge.
* `printImage(data[, options])` — décode les données d'un fichier PNG, JPEG, GIF, ou BMP et imprime le comme les données d'une carte.
* `status([jobId])`, `queue()`, `cancel(jobId)` — inspecte et gère les impressions dans la queue.
* `scan()` — scanne une page imprimée ou un livre Minecraft et retourne le contenu qui peut ensuite être utilisé avec `print`.
* `supplies()` — retourne la quantité de papier et d'encre, ainsi que la place disponible dans la sortie.
* `capabilities()` — retourne la versiond de l'API, les limites ainsi que les fonctionnalités supportées.

Les changements d'état de l'imprimante émèttent un signal `openprinter_job` qui contient l'addresse de l'imprimante, l'ID de l'impression dans la queue, l'état de l'imprimante et la raison du changement d'état.

## API OpenPrinter classique

Les programmes écrits pour l'API originelle d'OpenPrinter peuvent toujours utiliser l'ancienne API à tampon :

```lua
local printer = require("component").openprinter

printer.setTitle("Classic Page")
printer.writeln("Hello, world!")
printer.writeln("In color", 0x3366FF, "center")
printer.print()
```

Placez un livre et plume dans l'entrée à papier afin d'imprimer des pages dedans plutot que de produire des pages imprimées. Les impressions de livres acceptent seulement du texte simple de couleur noire, et le livre écrit produit est déplacé dans un des slots de sortie. Utilisez `printAndSign` avec le tampon classique pour le signer comme un livre vanilla :

```lua
printer.writeln("This is a book page.")
local job, reason = printer.printAndSign("My Book", "OpenPrinter")
assert(job, reason)
```

`printAndSign(title[, author][, copies])` utilises `OpenPrinter` comme nom d'auteur par défaut. Un livre écrit peut avoir jusqu'à 100 pages; `copies` permets d'ajouter le document dans le tampon plusieurs fois au même livre, ce qui corresponds au comportement de l'imprimante classique.

Les fonctions de compatibilités sont les suivantes : `writeln`, `setTitle`, `clear`, `print([copies])`, `getPaperLevel`, `getBlackInkLevel`, `getColorInkLevel`, `charCount`, `width`, `maxWidth`, `scanLine`, et `scanBook`.

Il est recommandé d'utiliser l'API moderne pour les nouveaux programmes parce que celle-ci supporte la mise en page, le système de queue d'impression, la gestion du status de l'imprimante, l'impression de cartes et d'images ainsi que la reprise automatique des impressions.

## Disquette OpenPrinter

La disquette verte **OpenPrinter** inclue des outils en ligne de commande pour OpenOS :

* `print`
* `printerstatus`
* `xerox`
* `printercopypage`
* `printmap`

Installez la disquette en utilisant l'installeur d'OpenOS.
