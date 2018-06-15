---
title: Trwa pobieranie informacji o stanie zadania Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać informacji o stanie zadań usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 22d7e5f0-94da-49b4-a1ac-dd4c14a423c2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.openlocfilehash: 13169716c47cf9389c8f2651393ac744441bdd6f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874010"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Trwa pobieranie informacji o stanie dla zadania importu/eksportu
Możesz wywołać [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_Get) operację pobierania informacji o importowanie i eksportowanie zadań. Zwracane informacje obejmują:

-   Bieżący stan zadania.

-   Przybliżona wartość procentowa, czy każde zadanie zostało zakończone.

-   Bieżący stan każdego dysku.

-   Identyfikatory URI obiektów blob zawierający dzienniki błędów i pełne rejestrowanie informacji (jeśli jest włączona).

W poniższych sekcjach opisano informacje zwracane przez `Get Job` operacji.

## <a name="job-states"></a>Stany zadania
Tabeli i na poniższym diagramie stanu opisują stany zadania przechodzi przez podczas cyklu życia. Bieżący stan zadania można ustalić wywołując `Get Job` operacji.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

W poniższej tabeli opisano każdy stan zadania mogą przechodzić przez.

|Stan zadania|Opis|
|---------------|-----------------|
|`Creating`|Po wywołaj operację Put zadania, tworzone jest zadanie i jego stan jest ustawiony `Creating`. W trakcie zadania `Creating` stanu usługi Import/Eksport przyjęto założenie, dyski nie zostały wysłane do centrum danych. Zadania mogą pozostać w `Creating` stanu przez maksymalnie dwa tygodnie, po których zostanie on automatycznie usunięty przez usługę.<br /><br /> Jeśli wywołanie operacji aktualizacji właściwości zadania w trakcie zadania `Creating` stanu zadania pozostaje w `Creating` stanie i interwał limitu czasu jest resetowany do dwóch tygodni.|
|`Shipping`|Po wysyłasz do pakietu, należy wywołać operację aktualizacji właściwości zadania aktualizacji stan zadania `Shipping`. Stan wysyłania można ustawić tylko wtedy, gdy `DeliveryPackage` (operator pocztowy i numer identyfikacyjny) i `ReturnAddress` skonfigurowane właściwości zadania.<br /><br /> Zadanie pozostanie w stanie wysyłania przez maksymalnie dwa tygodnie. Jeśli upłynie dwa tygodnie, a dyski nie zostały odebrane, otrzymasz powiadomienie operatory usługi Import/Eksport.|
|`Received`|Po otrzymaniu wszystkich dysków w centrum danych, stan zadania zostanie ustawiona do stanie Received.|
|`Transferring`|Po dyski zostały odebrane w centrum danych i co najmniej jeden dysk rozpoczął przetwarzanie, stan zadania zostanie ustawiona do `Transferring` stanu. Zobacz `Drive States` sekcji poniżej, aby uzyskać szczegółowe informacje.|
|`Packaging`|Po zakończeniu wszystkich dysków przetwarzania, zadanie zostanie umieszczona w `Packaging` stan do momentu dyski są wysyłane z powrotem do klienta.|
|`Completed`|Po wszystkie dyski zostały wysłane do klienta, jeśli zadanie zostało ukończone bez błędów, następnie zadanie jest równa `Completed` stanu. Zadania zostaną automatycznie usunięte po 90 dniach w `Completed` stanu.|
|`Closed`|Po wszystkie dyski zostały wysłane do klienta, jeśli pojawiły się błędy podczas przetwarzania zadania, następnie zadanie jest równa `Closed` stanu. Zadania zostaną automatycznie usunięte po 90 dniach w `Closed` stanu.|

Można anulować zadania tylko w określonych stanach. Anulowano zadanie pomija krok kopiowania danych, ale w przeciwnym razie wynika z tego samego przejścia stanów jako zadanie nie zostało anulowane.

W poniższej tabeli opisano błędy, które mogą wystąpić dla każdego stanu zadania, a także wpływ na zadania po wystąpieniu błędu.

|Stan zadania|Wydarzenie|Rozdzielczość / następne kroki|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Odebrano jeden lub więcej dysków dla zadania, ale zadanie nie ma na liście `Shipping` stanu lub nie istnieje rekord zadania w usłudze.|Zespół operacyjny usługi Import/Eksport podejmie próbę skontaktowania się z klientem można utworzyć ani zaktualizować zadania niezbędne informacje umożliwiające przejście zadania.<br /><br /> Jeśli zespołu operacji nie można nawiązać połączenia klienta w ciągu dwóch tygodni, zespół operacyjny podejmie próbę zwracać dyski.<br /><br /> W przypadku, gdy nie może być zwracany dysków i nie można nawiązać połączenia klienta, dyski zostaną bezpiecznie zniszczone w ciągu 90 dni.<br /><br /> Należy pamiętać, że zadania nie można przetworzyć do momentu jego stan zostanie zaktualizowany do `Shipping`.|
|`Shipping`|Nie odebrano pakiet dla zadania przez ponad dwa tygodnie.|Zespół operacyjny wyświetli powiadomienie klienta brakuje pakietu. W oparciu o odpowiedzi klienta, zespół operacyjny będzie wydłużyć okres oczekiwania na odebranie pakiet albo anulować zadanie.<br /><br /> W przypadku, gdy klient nie może nawiązać połączenia z lub nie odpowie w ciągu 30 dni, zespół operacyjny zainicjuje akcji, aby przenieść zadania z `Shipping` stanu bezpośrednio do `Closed` stanu.|
|`Completed/Closed`|Dyski nigdy osiągnięty adres zwrotny lub zostały uszkodzone wydania (dotyczy tylko zadania eksportu).|Jeśli nie osiągną adres zwrotny, klienta należy najpierw wywołaj operację pobrania zadania lub sprawdzenia stanu zadania w portalu, aby upewnić się, że dyski zostały wysłane. Jeśli dyski zostały wysłane, klienta kontaktowej dostawcy wysyłki i spróbuj zlokalizować dyski.<br /><br /> Jeśli dyski są uszkodzone podczas transportu, klienta możesz zażądać inne zadanie eksportu lub pobieranie brakujących obiektów blob.|
|`Transferring/Packaging`|Zadanie ma nieprawidłowe lub brakujące adres zwrotny.|Zespół operacyjny będzie dotrzeć do osoby kontaktowej dla zadania, aby uzyskać poprawny adres.<br /><br /> W przypadku, gdy klient jest nieosiągalny, dyski zostaną bezpiecznie zniszczone w ciągu 90 dni.|
|`Creating / Shipping/ Transferring`|Dysk, który nie ma na liście dysków do zaimportowania jest uwzględniony w pakiecie wysyłki.|Dodatkowych dysków nie będą przetwarzane i zostanie zwrócony do klienta po zakończeniu zadania.|

## <a name="drive-states"></a>Stany stacji
Tabeli i na poniższym diagramie opisują cyklu życia poszczególnych dyskach, ponieważ przechodzi ona za pomocą zadania importu lub eksportu. Możesz pobrać bieżący stan dysku przez wywołanie metody `Get Job` operacji i zapoznanie się `State` elementu `DriveList` właściwości.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

W poniższej tabeli opisano każdy stan może przekazywać dysku.

|Stan stacji|Opis|
|-----------------|-----------------|
|`Specified`|Dla zadania importu po utworzeniu zadania z operacją Put zadania stanu początkowego dla dysku jest `Specified` stanu. Dla zadania eksportu, ponieważ dysk nie zostanie wskazany po utworzeniu zadania dysk początkowy stan jest `Received` stanu.|
|`Received`|Dysk przechodzi do `Received` stanu, gdy operator usługi Import/eksport został przetworzony dysków, które zostały odebrane od firmy wysyłania dla zadania importu. Dla zadania eksportu jest dysk początkowy stan `Received` stanu.|
|`NeverReceived`|Dysk zostanie przeniesione do `NeverReceived` stanu po odebraniu pakietu dla zadania, ale pakiet nie zawiera dysku. Dysku można również przenosić w tym stanie, jeśli został dwa tygodnie od momentu usługa odebrała informacji dotyczących wysyłki, ale pakiet nie ma jeszcze dotarły centrum danych.|
|`Transferring`|Dysk zostanie przeniesione do `Transferring` stan, kiedy usługa zaczyna się na przesyłanie danych z dysku do systemu Windows Azure Storage.|
|`Completed`|Dysk zostanie przeniesione do `Completed` stan, kiedy usługa pomyślnie przekazywane wszystkie dane bez błędów.|
|`CompletedMoreInfo`|Dysk zostanie przeniesione do `CompletedMoreInfo` stanu, gdy usługa napotkał problemy podczas kopiowania danych z lub na dysku. Informacje mogą obejmować błędy, ostrzeżenia lub komunikaty informacyjne o zastępowaniu obiektów blob.|
|`ShippedBack`|Dysk zostanie przeniesione do `ShippedBack` stan, gdy zostały wydane z tyłu centrum danych na adres zwrotny.|

W poniższej tabeli opisano stanów awarii dysku i akcje wykonywane dla każdego stanu.

|Stan stacji|Wydarzenie|Rozdzielczość / następny krok|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Dysk, który jest oznaczony jako `NeverReceived` (ponieważ nie została odebrana jako część zadania wydania) dociera do innego wydania.|Zespół operacyjny przeniesie stacji `Received` stanu.|
|`N/A`|Dysk, który nie jest częścią wszystkie zadania dociera do centrum danych w ramach innego zadania.|Dysk zostanie oznaczona jako dodatkowy dysk i zostanie zwrócony do klienta po zakończeniu zadania skojarzone z oryginalnego pakietu.|

## <a name="faulted-states"></a>Stany błędnej
Gdy zadanie lub dysk nie powiodło się w trakcie cyklu życia oczekiwanego w normalny, zadania lub dysku zostaną przeniesione do `Faulted` stanu. W tym momencie zespół operacyjny skontaktuje się z klienta poczty e-mail lub telefonu. Po usunięciu problemu błędnej zadania lub dysku zostaną wykonane poza `Faulted` stanu i przenoszenia w odpowiednim stanie.

## <a name="next-steps"></a>Następne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
