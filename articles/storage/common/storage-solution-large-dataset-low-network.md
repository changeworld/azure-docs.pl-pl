---
title: Opcje transferu danych platformy Azure dla dużych zestawów danych o niskiej przepustowości sieci lub bez jej przepustowości| Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure do transferu danych, gdy masz ograniczoną do braku przepustowości sieci w danym środowisku i planujesz przenieść duże zestawy danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60730694"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Transfer dużych zestawów danych w przypadku niskiej przepustowości sieci lub jej braku
 
Ten artykuł zawiera omówienie rozwiązań transferu danych, gdy masz ograniczoną do żadnej przepustowości sieci w danym środowisku i planujesz przenieść duże zestawy danych. W tym artykule opisano również zalecane opcje transferu danych i odpowiednią macierz możliwości klucza dla tego scenariusza.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do [witryny Wybierz rozwiązanie do transferu danych platformy Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>Transfer offline lub transfer sieciowy

Duże zestawy danych oznaczają, że masz kilka TBs do kilku KB danych. Nie ma przepustowości sieci, sieć jest powolna lub jest zawodna. Ponadto:

- Koszty transferu sieciowego od dostawców usług internetowych (ISP) są ograniczone kosztami transferu sieciowego.
- Zabezpieczenia lub zasady organizacyjne nie zezwalają na połączenia wychodzące w przypadku danych poufnych.

We wszystkich powyższych przypadkach użyj urządzenia fizycznego, aby wykonać jednorazowy zbiorczy transfer danych. Wybierz spośród urządzeń Data Box Disk, Data Box, Data Box Heavy dostarczanych przez firmę Microsoft lub Importuj/eksportuj przy użyciu własnych dysków.

Aby sprawdzić, czy urządzenie fizyczne jest właściwą opcją, skorzystaj z poniższej tabeli. Pokazuje przewidywany czas przesyłania danych sieciowych dla różnych dostępnych przepustowości (przy założeniu 90% wykorzystania). Jeśli przewiduje się, że transfer sieciowy będzie zbyt wolny, należy użyć urządzenia fizycznego.  

![Transfer sieciowy lub transfer w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Zalecane opcje

Opcje dostępne w tym scenariuszu to urządzenia do transferu w trybie offline usługi Azure Data Box lub importu/eksportu platformy Azure.

- **Rodzina usługi Azure Data Box do przesyłania w trybie offline** — używaj urządzeń z urządzeń dostarczonych przez firmę Microsoft data box, aby przenieść duże ilości danych na platformę Azure, gdy jesteś ograniczony przez czas, dostępność sieci lub koszty. Kopiowanie danych lokalnych przy użyciu narzędzi, takich jak Robocopy. W zależności od rozmiaru danych przeznaczonych do przesyłania można wybrać opcję Dysk pola danych, Pole danych lub Data Box Heavy.
- **Importowanie/eksportowanie platformy** Azure — użyj usługi Importowanie/Eksportowanie platformy Azure przez wysłanie własnych dysków, aby bezpiecznie importować duże ilości danych do magazynu obiektów Blob platformy Azure i plików platformy Azure. Ta usługa może również służyć do przesyłania danych z magazynu obiektów Blob platformy Azure na dyski i wysyłać do witryn lokalnych.

## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli podsumowano różnice w kluczowych możliwościach.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Rozmiar danych                        |    Do 35 TB                 |    Do 80 kb/s na urządzenie                       |    Do 800 TB na urządzenie               |    Zmienna                            |
|    Typ danych                        |    Obiekty blob platformy Azure                  |    Obiekty blob platformy Azure<br>Azure Files                    |    Obiekty blob platformy Azure<br>Azure Files            |    Obiekty blob platformy Azure<br>Azure Files          |
|    Faktor                      |    5 SSD na zamówienie             |    1 X 50 funtów. urządzenie o rozmiarze pulpitu na zamówienie    |    1 X ~500 funtów. duże urządzenie na zamówienie    |    Do 10 dysków twardych/dysków SSD na zamówienie        |
|    Początkowy czas instalacji               |    Małe <br>(15 min.            |    Od niskiego do umiarkowanego <br> (<30 min)               |    Średni<br>(1-2 godziny)               |    Umiarkowane lub trudne<br>(zmienna) |
|    Wysyłanie danych na platformę Azure               |    Tak                          |    Tak                                           |    Tak                                   |    Tak                                 |
|    Eksportowanie danych z platformy Azure           |    Nie                           |    Nie                                            |    Nie                                    |    Tak                                 |
|    Szyfrowanie                       |    AES 128-bitowy                  |    AES 256-bitowy                                   |    AES 256-bitowy                           |    AES 128-bitowy                         |
|    Sprzęt                         |     Firma Microsoft dostarczyła          |    Firma Microsoft dostarczyła                            |    Firma Microsoft dostarczyła                    |    Dostarczony klient                   |
|    Interfejs sieciowy                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integracja z partnerami              |    Niektóre                         |    [Wysokiej](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Wysokiej](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Niektóre                                |
|    Wysyłka                         |    Zarządzane przez firmę Microsoft            |    Zarządzane przez firmę Microsoft                             |    Zarządzane przez firmę Microsoft                     |    Zarządzane przez klienta                    |
| Używanie podczas przenoszenia danych         |W granicach handlu|W granicach handlu|W granicach handlu|Ponad granicami geograficznymi, np.|
|    Cennik                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak

    - [Przesyłanie danych za pomocą dysku data box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Przesyłanie danych za pomocą pola danych](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Przesyłanie danych za pomocą pliku Import/Eksport](/azure/storage/common/storage-import-export-data-to-blobs).
