---
title: Informacje o wersji platformy Azure Data Box bramy ogólnodostępnej | Dokumentacja firmy Microsoft
description: W tym artykule opisano krytyczne nierozwiązane problemy i rozwiązania dla bramy Azure pole danych, w wersji ogólnodostępnej.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60754203"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Informacje o wersji platformy Azure dane pole Edge/Azure dane pole bramy ogólnodostępnej

## <a name="overview"></a>Omówienie

W poniższych informacjach o Zidentyfikuj krytyczne nierozwiązane problemy i rozwiązane problemy dla ogólna dostępność (GA) wersji dla usługi Azure Data Box Edge i bramy pola danych platformy Azure.

Informacje o wersji są ciągle aktualizowane, a ponieważ są wszystkie wykrywane krytyczne problemy wymagające obejścia są dodawane. Przed wdrożeniem bramy okno usługi Edge i dane pole danych, należy uważnie przeczytaj informacje zawarte w informacjach o wersji.

Wersja Ogólnodostępna odnosi się do wersji oprogramowania:

- **Brama pola danych 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Co nowego

- **Nowe obrazy dysku wirtualnego** -nowego dysku VHDX i VMDK są teraz dostępne w witrynie Azure portal. Pobierz te obrazy do aprowizowanie, konfigurowanie i wdrażanie nowych urządzeń bramy pola danych wersji ogólnie dostępnej. Urządzenia bramy pola danych, utworzony w starszej wersji zapoznawczej, nie można zaktualizować wersji do tej wersji. Aby uzyskać więcej informacji, przejdź do [przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).
- **Obsługa systemu plików NFS** — Obsługa systemu plików NFS jest obecnie w wersji zapoznawczej i jest dostępna w wersji 3.0 i 4.1 klientów uzyskujących dostęp do urządzeń krawędź pola danych i bramy pola danych.
- **Odporność magazynu** — urządzenia usługi Edge pole danych może wytrzymać awarii dysk z danymi za pomocą funkcji odporność magazynu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Odporność magazynu można włączyć, zaznaczając **odporne na błędy** opcji **ustawienia magazynu** lokalnej interfejs użytkownika sieci web.


## <a name="known-issues-in-ga-release"></a>Znane problemy w wersji ogólnie dostępnej wersji

Poniższa tabela zawiera podsumowanie znanych problemów występujących dla bramy pola danych z wersją.

| Nie. | Cecha | Problem | Obejście/komentarzy |
| --- | --- | --- | --- |
| **1.** |Typy plików | Nie są obsługiwane następujące typy plików: znak plików bloku, gniazda, potoki, plików łącza symbolicznego.  |Kopiowanie tych plików wyników w plikach długość 0 tworzonych na systemu plików NFS jest udostępnianie. Te pliki pozostają w stanie błędu i są również zgłaszane w *error.xml*. <br> Linki symboliczne z katalogami spowodować katalogi, które nigdy nie wprowadzenie oznaczone w trybie offline. W rezultacie nie widać szarego między na katalogi, które wskazuje, że katalogi są w trybie offline i powiązanej zawartości całkowicie został przekazany na platformę Azure. |
| **2.** |Usuwanie | Z powodu błędu w tej wersji usunięcie udziału NFS następnie udział mogą nie zostać usunięte. Będzie wyświetlany stan udziału *usuwanie*.  |Dzieje się tak, tylko wtedy, gdy udział jest przy użyciu nazwy pliku nieobsługiwany. |
| **3.** |Kopiuj | Kopiowanie danych kończy się niepowodzeniem z powodu błędu:  Nie można ukończyć żądanej operacji ze względu na ograniczenia systemu plików.  |Alternatywne Data Stream (ADS) skojarzony z plików większych niż 128 KB nie jest obsługiwane.   |


## <a name="next-steps"></a>Kolejne kroki

- [Przygotowanie do wdrożenia usługi Azure Gateway pole danych](data-box-gateway-deploy-prep.md).
- [Przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).
