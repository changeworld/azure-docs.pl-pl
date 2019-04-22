---
title: Opcje dla dużych zestawów danych za pomocą niski lub nie przepustowości transferu danych systemu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie na platformie Azure do transferu danych, gdy masz ograniczone do nie przepustowości sieci w Twoim środowisku i planujesz przeniesienie dużych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 4c4ac9489b9613b2eeaf26a3df9f4cbc664a1026
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58849615"
---
# <a name="data-transfer-for-large-datasets-with-low-or-no-network-bandwidth"></a>Transfer danych w przypadku dużych zestawów danych za pomocą niski lub nie przepustowość sieci
 
Ten artykuł zawiera omówienie transferu danych rozwiązania, gdy masz ograniczone do nie przepustowości sieci w Twoim środowisku i planujesz przeniesienie dużych zestawów danych. Opisano również opcje transferu danych zalecany i macierzy odpowiedniego kluczowe możliwości, w tym scenariuszu.

Aby dowiedzieć się z omówieniem wszystkich opcji transferu danych o dostępności, przejdź do [wybierz rozwiązanie transferu danych systemu Azure](storage-choose-data-transfer-solution.md).

## <a name="offline-transfer-or-network-transfer"></a>W trybie offline transferu transferu lub sieci

Duże zestawy danych oznacza, że masz kilka TB do kilku PBs danych. Masz ograniczone do nie przepustowość sieci, sieci działa wolno lub jest nieprzewidywalne. Ponadto:

- Koszty transferu sieciowego są ograniczeni od dostawcy usług internetowych (ISP).
- Zabezpieczenia lub zasady organizacyjne nie zezwalają na połączenia wychodzące podczas pracy z danymi poufnymi.

We wszystkich powyższych przypadkach korzystanie z urządzenia fizycznego w celu jednorazowego zbiorczego transferu danych. Wybierz z dysku Data Box, urządzenia Data Box duże pole danych urządzenia, które są dostarczane przez firmę Microsoft lub Import/Export, za pomocą własnych dysków.

Aby sprawdzić, czy urządzenia fizycznego jest odpowiedniej opcji, skorzystaj z poniższej tabeli. Prezentuje przewidywany czas przesyłania danych z sieci dla różnych dostępnych przepustowości (przy założeniu 90% wykorzystania). Jeśli transfer sieciowy jest przewidywane jest zbyt wolny, należy użyć urządzenia fizycznego.  

![Przekazanie sieci lub w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

## <a name="recommended-options"></a>Zalecane opcje

Opcje dostępne w tym scenariuszu są urządzenia Azure Data Box transferu w trybie offline lub Azure Import/Export.

- **Azure Data Box rodziny transferów w trybie offline** — przenoszenie dużych ilości danych na platformę Azure, gdy są ograniczone przez czas, dostępność sieci lub koszty przy użyciu urządzeń z urządzeń, urządzenia Data Box dostarczany przez firmę Microsoft. Kopiowanie danych lokalnych za pomocą narzędzi, takich jak Robocopy. W zależności od rozmiaru danych, przeznaczonych do przeniesienia możesz wybrać z dysku Data Box, urządzenia Data Box lub duże pole danych.
- **Usługa Azure Import/Export** — usługi użycia usługi Azure Import/Export za wysyłanie własnych dysków twardych do bezpiecznego importowania dużych ilości danych do usługi Azure Blob storage i Azure Files. Ta usługa może również przenoszenia danych z usługi Azure Blob storage do stacji dysków do wysłania do lokacji lokalnej.

## <a name="comparison-of-key-capabilities"></a>Porównanie możliwości klucza

W poniższej tabeli podsumowano różnice w kluczowych funkcji.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy              |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Rozmiar danych                        |    Maksymalnie 35 TB                 |    Do 80 TB na każdym urządzeniu                       |    Maksymalnie 800 TB na każdym urządzeniu               |    Zmienna                            |
|    Typ danych                        |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    Współczynnik postaci                      |    5 dysków SSD na zamówienie zlecenie             |    1 x 50-modułów równoważenia obciążenia. rozmiar pulpitu urządzenia na zamówienie zlecenie    |    Kg — 1 X ~ 500. duże urządzenia na zamówienie zlecenie    |    Do 10 dysków twardych/SSD na zamówienie zlecenie        |
|    Czas początkowej konfiguracji               |    Małe <br>(15 min)            |    Działania niskiej lub średniej <br> (< 30 minut)               |    Średnia<br>(1 – 2 godziny)               |    Moderowanie trudne do<br>(zmienna) |
|    Wysyłanie danych do platformy Azure               |    Yes                          |    Yes                                           |    Yes                                   |    Yes                                 |
|    Eksportowanie danych z platformy Azure           |    Nie                           |    Nie                                            |    Nie                                    |    Yes                                 |
|    Szyfrowanie                       |    AES 128-bitowy                  |    AES 256-bitowy                                   |    AES 256-bitowy                           |    AES 128-bitowy                         |
|    Sprzęt                         |     Podany firmy Microsoft          |    Podany firmy Microsoft                            |    Podany firmy Microsoft                    |    Dostarcza użytkownik                   |
|    Interfejs sieciowy                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integracja z partnerami              |    Niektóre                         |    [Wysoka](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Wysoka](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Niektóre                                |
|    Wysyłka                         |    Zarządzany przez firmę Microsoft            |    Zarządzany przez firmę Microsoft                             |    Zarządzany przez firmę Microsoft                     |    Zarządzaną przez klienta                    |
| Stosowane, gdy dane są przenoszone         |W granicach handlowych|W granicach handlowych|W granicach handlowych|W granicach geograficznych, np. nam UE|
|    Cennik                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


## <a name="next-steps"></a>Kolejne kroki

- Zrozumienie sposobu

    - [Transferowanie danych za pomocą dysku Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferowanie danych za pomocą urządzenia Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
    - [Transferowanie danych za pomocą Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
