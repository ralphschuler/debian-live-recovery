# Debian Recovery Live USB

Dieses Repository ermöglicht den Bau eines Debian‑Live‑Systems, das als universelles
Recovery‑ und Administrationswerkzeug dient. Es basiert auf der aktuellen stabilen
Debian‑Distribution („tr ixie“/Debian 13)【335699653578159†L26-L29】. Durch die Unterstützung der
Architekturen `amd64` und `i386` lässt sich der Stick auch auf sehr alten Rechnern
oder in 32‑Bit‑EFI‑Umgebungen einsetzen.

## Inhalt

Das Live‑System enthält verschiedene Tools für Systemrettung, Dateisystem‑Reparaturen
und Netzwerkanalyse. Zu den wichtigsten Paketen gehören GParted, fsarchiver,
GNU ddrescue, TestDisk, Photorec, NTFS‑3G, Memtest, rsync, lvm2 und verschiedene
Netzwerk‑Utilities【902333962684394†L45-L57】【902333962684394†L103-L124】. Weitere nützliche
Werkzeuge wie `htop`, `mc`, `nano`, `vim` und `smartmontools` sind ebenfalls enthalten.

## Build‑Prozess

Das Build‑System nutzt `live‑build`. Das Skript `auto/config` erzeugt die Konfiguration
für die angegebene Architektur (standardmäßig `amd64`), setzt die Distribution auf
`trixie`, aktiviert `iso-hybrid`‑Images und schaltet die Archive für `contrib`,
`non‑free` und `non‑free‑firmware` frei. Ein Aufruf von `lb build` liest anschließend
die Konfiguration und erstellt das ISO‑Image【843226263871971†L2105-L2141】【843226263871971†L2143-L2147】.

### Voraussetzungen

Zum Bauen auf einem Debian‑ oder Ubuntu‑System werden folgende Pakete benötigt:

```bash
sudo apt-get update
sudo apt-get install live-build
```

### Konfiguration und Build

```bash
# Konfiguration für amd64
ARCH=amd64 ./auto/config
sudo lb build
mv live-image-amd64.hybrid.iso debian-live-recovery-amd64.iso

# Konfiguration für i386
ARCH=i386 ./auto/config
sudo lb build
mv live-image-i386.hybrid.iso debian-live-recovery-i386.iso
```

Das Ergebnis sind zwei ISO‑Dateien, die jeweils ein Hybridsystem enthalten. Hybrid‑ISOs
können direkt mittels `cp` auf einen USB‑Stick geschrieben werden. Ersetzen Sie
`/dev/sdX` durch das richtige Gerät:

```bash
sudo cp debian-live-recovery-amd64.iso /dev/sdX && sync
```

Der Inhalt des Sticks wird vollständig überschrieben【843226263871971†L1433-L1447】. Um ungenutzten
Speicherplatz auf dem Stick zu verwenden, kann danach eine zweite Partition mit
`gparted` oder `parted` angelegt werden【843226263871971†L1451-L1484】.

## Unterstützung für 32‑Bit‑EFI

Viele ältere Rechner, insbesondere manche Core 2 Duo‑Macs, besitzen nur einen
32‑Bit‑EFI‑Bootloader, obwohl sie 64‑Bit‑Kernels ausführen können【863506069576672†L14-L18】. Um das
64‑Bit‑ISO auf solchen Geräten zu booten, muss die Datei `bootia32.efi` in das
Verzeichnis `EFI/BOOT` des ISO‑Images kopiert werden【863506069576672†L133-L144】. Diese Datei
wird durch das Paket `grub-efi-ia32-bin` bereitgestellt. Nach dem Erstellen des
ISOs können Sie das Image mounten und die Datei einfügen:

```bash
sudo apt-get install grub-efi-ia32-bin
# mount the ISO
mkdir /mnt/iso
sudo mount debian-live-recovery-amd64.iso /mnt/iso
# Kopieren Sie bootia32.efi in EFI/BOOT des ISO (erfordert SquashFS‑Werkzeuge und ISO‑Editor).
```

Alternativ können Sie beide ISOs auf einen Ventoy‑Stick legen. Ventoy erkennt
automatisch das passende Image und ermöglicht das Starten auf 32‑ und
64‑Bit‑Systemen.

## Automatische Releases

Im Verzeichnis `.github/workflows` befindet sich eine GitHub‑Actions‑Konfiguration.
Bei jeder Änderung im `main`‑Branch werden die ISOs für `amd64` und `i386` gebaut
und als Artefakt bereitgestellt. Sie können die Workflows anpassen, um die ISOs
auf GitHub‑Releases hochzuladen.

## Lizenz

Dieses Projekt steht unter der GNU General Public License v3 (oder später).
