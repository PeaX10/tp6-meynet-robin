# TP6 - Gestion des disques / Tâches d’administration
MEYNET Léo - DEVILLEBICHOT Robin

## Exercice 1: Disques et partitions
  > 1. Dans l’interface de configuration de votre VM, créez un second disque dur, de 5 Go dynamiquement alloués; puis démarrez la VM.
  > 2. Vérifiez que ce nouveau disque dur est bien détecté par le système.
  > 3. Partitionnez ce disque en utilisant `fdisk`: créez une première partition de 2 Go de type Linux(n°83), et une seconde partition de 3 Go en NTFS(n°7).
  
  > 4. A ce stade, les partitions ont été créées, mais elles n’ont pas été formatées avec leur système de fichiers.A l’aide de la commandemkfs, formatez vos deux partitions (pensez à consulter le manuel!).
  
  > 5. Pourquoi la commandedf -T, qui affiche le type de système de fichier des partitions, ne fonctionne-t-elle pas sur notre disque?
  
  > 6. Faites en sorte que les deux partitions créées soient montées automatiquement au démarrage de lamachine, respectivement dans les points de montage/dataet/win(vous pourrez vous passer desUUID en raison de l’impossibilité d’effectuer des copier-coller).
  
  > 7. Utilisez la commandemountpuis redémarrez votre VM pour valider la configuration.
  
  > 8. Montez votre clé USB dans la VM.
  > 9. Créez un dossier partagé entre votre VM et votre système hôte (rem. il peut être nécessaire d’installer les Additions invité de VirtualBox.

## Exercice 2: Partitionnement LVM
  Dans cet exercice, nous allons aborder le partitionnement LVM, beaucoup plus flexible pour manipuler les disques et les partitions.
  > 1. On va réutiliser le disque de 5 Gio de l’exercice précédent. Commencez par démonter les systèmes de fichiers montés dans/dataet/wins’ils sont encore montés, et supprimez les lignes correspondantes du fichier/etc/fstab
  > > 2. Supprimez les deux partitions du disque, et créez une patition unique de type LVMLa création d’une partition LVM n’est pas indispensable, mais vivement recommandée quandon utilise LVM sur un disque entier. En effet, elle permet d’indiquer à d’autres OS ou logiciels degestion de disques (qui ne reconnaissent pas forcément le format LVM) qu’il y a des données surce disque.Attention à ne pas supprimer la partition système!
  > 3. A l’aide de la commandepvcreate, créez unvolume physiqueLVM. Validez qu’il est bien créé, enutilisant la commandepvdisplay.Toutes les commandes concernant les volumes physiques commencent parpv. Celles concernantles groupes de volumes commencent parvg, et celles concernant les volumes logiques parlv.
  > 4. A l’aide de la commandevgcreate, créez ungroupe de volumes, qui pour l’instant ne contiendra quele volume physique créé à l’étape précédente. Vérifiez à l’aide de la commandevgdisplay.Par convention, on nomme généralement les groupes de volumesvgxx(où xx représente l’indicedu groupe de volume, en commençant par 00, puis 01...)Si tout s’est bien passé, vous devriez voir un nouveau dossier du nom de votre groupe de volumesdans/dev.
  > 5. Créez un volume logique appelélvDataoccupant l’intégralité de l’espace disque disponible.On peut renseigner la taille d’un volume logique soit de manière absolue avec l’option-L(parexemple-L 10Gpour créer un volume de 10 Gio), soit de manière relative avec l’option-l:-l60%VGpour utiliser 60% de l’espace total du groupe de volumes, ou encore-l 100%FREEpourutiliser la totalité de l’espace libre.
  > 6. Dans ce volume logique, créez une partition que vous formaterez en ext4, puis procédez comme dansl’exercice 1 pour qu’elle soit montée automatiquement, au démarrage de la machine, dans/data.A ce stade, l’utilité de LVM peut paraître limitée. Il trouve tout son intérêt quand on veut parexemple agrandir une partition à l’aide d’un nouveau disque.
  > 7. Eteignez la VM pour ajouter un second disque (peu importe la taille pour cet exercice). Redémarrezla VM, vérifiez que le disque est bien présent. Puis, répétez les questions 2 et 3 sur ce nouveau disque.
  > 8. Utilisez la commandevgextend <nom_vg> <nom_pv>pour ajouter le nouveau disque au groupe devolumes
  > 9. Utilisez la commandelvresize(oulvextend) pour agrandir le volume logique. Enfin, il ne faut pasoublier de redimensionner le système de fichiers à l’aide de la commanderesize2fs.

## Exercice 3: Exécution de commandes en différé : 'at' et 'cron'
