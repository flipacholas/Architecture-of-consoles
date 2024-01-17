---
title: A játékkonzolok felépítése
description: A játékkonzolok fejlődésének bemutatása a felépítésük alapján
id: consoles
layout: consoles
seo_image: banner.png
cascade:
  description: "Egy játékkonzol belső működésének alapos elemzése"
  second_title: Gyakorlati elemzés
  layout: console
  type: console
  image_dir: consoles
  icons:
    - "resources"
  seo_image: diagram.png
  seo_type: TechArticle
#Historical
aliases:
  - ../projects/consoles
---

{{< linked_img src="banner.png" alt="A játékkonzolok felépítése" no_link="true" no_lazyload="true" >}}

A játékkonzolok fejlődése lenyűgöző. Míg a hagyományos PC-k "fokozatosan" fejlődtek, a játékkonzolok új generációi radikálisan új módon működtek. Ami itt következik, az egy cikksorozat, ami remélhetőleg feltárja az új technológiai trendek mögött meghúzódó okokat. Azt is érthetővé teszi, hogy miért nem lehet egyes rendszereket "bitekkel", megahertzekkel, a RAM mennyiségével vagy egyéb technikai jellemzőkkel összefoglalni.

Ez nem egy fejlesztői kézikönyv, csak egy *alapos* bevezetés ahhoz, hogy hogyan is működnek ezek a rendszerek belül. Vedd figyelembe, hogy ez a technológia rendkívül bonyolult lett az utóbbi időben, úgyhogy ha nehezen tudod követni a későbbi cikkeket, először próbáld megérteni a korai darabok leírásában szereplő infókat. Ezek sok olyan koncepciót és meghatározást mutatnak be, amelyek folyamatosan fejlődtek az idők során.

A számítástechnika alapvető ismerete előnyt jelent - ettől függetlenül rendkívül sok energiát fektettem abba, hogy szélesebb közönség számára is értelmezhető legyen ez a cikksorozat, úgyhogy nyugodtan próbáld meg! De ha esetleg mégis elakadnál, esetleg nézd meg a [kiegészítő olvasmányok]({{< ref "readings" >}}) átnézése után.

Bár törekedtem a pontosságra, előfordulhatnak hibák, ha ilyet találsz {{< email "dobj egy levelet" >}} vagy {{< consoles/repo_link "javasolj változtatásokat" >}}. Végül pedig, ha inkább eBook olvasón szeretsz olvasni, nézd meg az [eBook kiadást]({{< ref "ebook" >}}).

Minden további szószaporítás nélkül: itt kezdődik!
