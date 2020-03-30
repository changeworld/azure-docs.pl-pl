---
title: Informacje o ogólnej dostępności bramy danych usługi Azure Data Box| Dokumenty firmy Microsoft
description: W tym artykule opisano krytyczne otwarte problemy i rozwiązania bramy azure data box z uruchomieniu ogólnej dostępności.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265404"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Informacje o ogólnej dostępności usługi Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Omówienie

W poniższych informacjach o wersji identyfikowano krytyczne otwarte problemy i rozwiązane problemy dotyczące wydania ogólnej dostępności (GA) dla usługi Azure Data Box Edge i bramy azure data box gateway.

Informacje o wersji są stale aktualizowane, a ponieważ wykrywane są krytyczne problemy wymagające obejścia, są dodawane. Przed wdrożeniem bramy data box edge/data box należy dokładnie przejrzeć informacje zawarte w informacjach o wersji.

Wersja GA odpowiada wersjom oprogramowania:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Co nowego?

- **Nowe obrazy dysków wirtualnych** — nowe obrazy VHDX i VMDK są teraz dostępne w witrynie Azure portal. Pobierz te obrazy, aby aprowizować, konfigurować i wdrażać nowe urządzenia GA bramy data box. Nie można zaktualizować urządzeń bramy pól danych utworzonych we wcześniejszych wersjach w wersji zapoznawczej do tej wersji. Aby uzyskać więcej informacji, przejdź do [witryny Przygotowanie do wdrożenia bramy usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Obsługa systemu plików NFS** — obsługa systemu plików NFS jest obecnie dostępna w wersji zapoznawczej i jest dostępna dla klientów w wersji 3.0 i v4.1, którzy uzyskują dostęp do urządzeń Data Box Edge i Data Box Gateway.
- **Odporność pamięci masowej** — urządzenie Data Box Edge może wytrzymać awarię jednego dysku danych dzięki funkcji odporności magazynu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Odporność magazynu można włączyć, wybierając opcję **Odporność** w **ustawieniach magazynu** w lokalnym interfejsie użytkownika sieci web.


## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji GA

Poniższa tabela zawiera podsumowanie znanych problemów dla bramy data box uruchomionej wersji.

| Nie. | Funkcja | Problem | Obejście/komentarze |
| --- | --- | --- | --- |
| **1.** |Typy plików | Następujące typy plików nie są obsługiwane: pliki znaków, pliki blokowe, gniazda, potoki, łącza symboliczne.  |Kopiowanie tych plików powoduje utworzenie plików o długości 0 w udziale NFS. Pliki te pozostają w stanie błędu i są również zgłaszane w *error.xml*. <br> Symboliczne linki do katalogów powodują, że katalogi nigdy nie są oznaczane w trybie offline. W rezultacie może nie być widoczny szary krzyżyk w katalogach, który wskazuje, że katalogi są w trybie offline i wszystkie skojarzone treści został całkowicie przekazany do platformy Azure. |
| **2.** |Usunięcie | Z powodu błędu w tej wersji, jeśli udział NFS zostanie usunięty, udział nie może zostać usunięty. Stan udziału wyświetli *usuwanie*.  |Dzieje się tak tylko wtedy, gdy udział używa nieobsługiwał nazwy pliku. |
| **3.** |Copy | Kopiowanie danych kończy się niepowodzeniem z błędem: Nie można ukończyć żądanej operacji z powodu ograniczenia systemu plików.  |Alternatywny strumień danych (ADS) skojarzony z rozmiarem pliku większym niż 128 KB nie jest obsługiwany.   |


## <a name="next-steps"></a>Następne kroki

- [Przygotuj się do wdrożenia bramy azure data box](data-box-gateway-deploy-prep.md).
- [Przygotuj się do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).
