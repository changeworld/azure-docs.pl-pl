---
title: Trwa pobieranie informacji o stanie dla zadania usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać informacje o stanie dla zadania usługi Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/16/2016
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 1a878b5a9f0502ff9acd411359895d7431fb76f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478678"
---
# <a name="retrieving-state-information-for-an-importexport-job"></a>Trwa pobieranie informacji o stanie dla zadania importu/eksportu
Możesz wywołać [pobrania zadania](/rest/api/storageimportexport/jobs) operację pobierania informacji na temat zarówno importowania i eksportowania zadania. Zwracane informacje obejmują:

-   Bieżący stan zadania.

-   Przybliżony wartość procentowa, że każde zadanie zostało ukończone.

-   Bieżący stan każdego dysku.

-   Identyfikatory URI obiektów blob zawierający dzienniki błędów i pełne rejestrowanie informacji (jeśli jest włączona).

W poniższych sekcjach opisano informacje zwrócone przez `Get Job` operacji.

## <a name="job-states"></a>Stany zadań
W tabeli i Poniższy diagram stanu opisują stanów, które zadanie przechodzi przez podczas cyklu życia. Można określić bieżący stan zadania, wywołując `Get Job` operacji.

![JobStates](./media/storage-import-export-retrieving-state-info-for-a-job/JobStates.png "JobStates")

W poniższej tabeli opisano każdy stan, że zadania może być przekazywane.

|Stan zadania|Opis|
|---------------|-----------------|
|`Creating`|Po wywołaj operację Put zadania, tworzone jest zadanie i jego stan jest ustawiony na `Creating`. Podczas wykonywania zadania w `Creating` stanu usługi Import/Export przyjęto założenie, dyski nie zostały wysłane do centrum danych. Zadanie może pozostać w `Creating` stanie do dwóch tygodni, po których jest on automatycznie usunięty przez usługę.<br /><br /> Jeśli wywołanie operacji właściwości zadania aktualizacji podczas wykonywania zadania w `Creating` stanu zadania pozostaje w `Creating` stanie i interwał limitu czasu jest resetowany do dwóch tygodni.|
|`Shipping`|Po wysyłasz pakietu, należy wywołać aktualizacji operacji właściwości zadania aktualizacji stan zadania `Shipping`. Stan wysyłania można ustawić tylko wtedy, gdy `DeliveryPackage` (operatora pocztowego i numer identyfikacyjny) i `ReturnAddress` właściwości zostały ustawione dla zadania.<br /><br /> Zadanie będzie pozostawać w stanie wysyłania przez maksymalnie dwa tygodnie. Jeśli dyski nie zostały odebrane przeszły dwa tygodnie, powiadomimy Cię, Operatorzy usługi Import/Export.|
|`Received`|Po otrzymaniu wszystkich dysków w centrum danych, stan zadania ustawi stan odebrane.|
|`Transferring`|Po dyski zostały odebrane w centrum danych i co najmniej jeden dysk rozpoczął przetwarzanie, stan zadania zostanie ustawiony na `Transferring` stanu. Zobacz `Drive States` sekcji poniżej, aby uzyskać szczegółowe informacje.|
|`Packaging`|Po wszystkich dysków przetwarzania, zadania zostaną umieszczone w `Packaging` stan do momentu dyski są dostarczane z powrotem do klienta.|
|`Completed`|Po wszystkie dyski zostały wysłane do klienta, jeśli zadanie zostało ukończone bez błędów, następnie zadania zostaną ustawione `Completed` stanu. Zadania zostaną automatycznie usunięte po upływie 90 dni w `Completed` stanu.|
|`Closed`|Po wszystkie dyski zostały wysłane do klienta, jeśli występowały błędy podczas przetwarzania zadania, następnie zadania zostaną ustawione `Closed` stanu. Zadania zostaną automatycznie usunięte po upływie 90 dni w `Closed` stanu.|

Można anulować zadania tylko w określonych stanach. Anulowano zadanie pominie krok kopiowania danych, ale w przeciwnym razie jest zgodna z ten sam stanami jako zadanie, które nie zostało anulowane.

W poniższej tabeli opisano błędy, które mogą wystąpić dla każdego stanu zadania, jak również wpływu na zadania, po wystąpieniu błędu.

|Stan zadania|Wydarzenie|Rozpoznawanie / następne kroki|
|---------------|-----------|------------------------------|
|`Creating or Undefined`|Odebrano jeden lub więcej dysków do danego zadania, ale zadanie nie znajduje się w `Shipping` stanu lub nie ma rekordu zadania w usłudze.|Zespół operacyjny usługi Import/Export będzie podejmować próby skontaktowania się z klientem można utworzyć ani zaktualizować zadania niezbędne informacje umożliwiające przejście zadania do przodu.<br /><br /> Jeśli zespół operacyjny nie może skontaktować się z klienta w ciągu dwóch tygodni, zespół operacyjny będzie podejmować próby zwracają dysków.<br /><br /> W przypadku, gdy nie można zwrócić dysków i nie można nawiązać połączenia klienta, dyski będą bezpiecznie zniszczone w ciągu 90 dni.<br /><br /> Należy pamiętać, że zadania nie można przetworzyć, do momentu jego stan zostanie zaktualizowany w celu `Shipping`.|
|`Shipping`|Pakiet dla zadania nie jest już dostępna dla ponad dwa tygodnie.|Zespół operacyjny powiadomi klienta brakujących pakietów. Oparte na odpowiedzi klienta, zespół operacyjny będzie wydłużyć okres oczekiwania dla pakietu dostarczenie albo anulować zadanie.<br /><br /> W przypadku, gdy klient nie może skontaktować się z lub nie odpowie w ciągu 30 dni, zespół operacyjny będzie inicjował akcji, aby przenieść zadania z `Shipping` bezpośrednio do stanu `Closed` stanu.|
|`Completed/Closed`|Dyski nigdy osiągnięty adres zwrotny lub zostały uszkodzone w wysyłce (dotyczy tylko przez zadanie eksportu).|Jeśli dyski nie osiągają adres zwrotny, klienta należy najpierw wywołać operację pobrania zadania lub sprawdzić stan zadania w portalu, aby upewnić się, że dyski zostały wydane. Jeśli dyski zostały wysłane, następnie odbiorcy powinni skontaktować się dostawca wysyłkę Znajdź dyski i spróbuj.<br /><br /> Jeśli dyski zostały uszkodzone podczas wydania, klient może być do żądania innego zadania eksportu lub pobieranie brakujących obiektów blob.|
|`Transferring/Packaging`|Zadanie zawiera nieprawidłowe lub Brak adresu zwrotnego.|Zespół operacyjny będzie korzystał z osoba kontaktowa dla zadania można uzyskać poprawny adres.<br /><br /> W przypadku, gdy klient nie można nawiązać połączenia, dyski będą bezpiecznie zniszczone w ciągu 90 dni.|
|`Creating / Shipping/ Transferring`|Dysk, który nie jest widoczna na liście dysków, które mają zostać zaimportowane znajduje się w pakiecie wysyłki.|Dodatkowe dyski nie zostaną przetworzone i zostanie zwrócony do klienta, gdy zadanie jest ukończone.|

## <a name="drive-states"></a>Stany dysków
W tabeli i na poniższym diagramie opisują cyklu życia poszczególnych dyskach, który został przechodzi przez zadania importu lub eksportu. Możesz pobrać bieżący stan dysku przez wywołanie metody `Get Job` operacji i zapoznanie się `State` elementu `DriveList` właściwości.

![DriveStates](./media/storage-import-export-retrieving-state-info-for-a-job/DriveStates.png "DriveStates")

W poniższej tabeli opisano każdy stan, że dysk może być przekazywane.

|Stan stacji|Opis|
|-----------------|-----------------|
|`Specified`|Do zadania importu, gdy zadanie jest tworzone za pomocą operacji Put zadania stanu początkowego dla dysku jest `Specified` stanu. Przez zadanie eksportu, ponieważ dysk nie zostanie określony po utworzeniu zadania dysk początkowy stan jest `Received` stanu.|
|`Received`|Dysk przechodzi do `Received` stanu, gdy operator usługi Import/Export został przetworzony dysków, które zostały odebrane z firmą transportową do zadania importu. Przez zadanie eksportu jest stan stacji początkowej `Received` stanu.|
|`NeverReceived`|Dysk zostanie przeniesione do `NeverReceived` stanu po odebraniu pakietu do danego zadania, ale pakiet nie zawiera dysku. Dysk można również przenosić w tym stanie, jeśli został dwa tygodnie, ponieważ usługa otrzymała informacje o wysyłce, ale pakiet nie dotarła jeszcze w centrum danych.|
|`Transferring`|Dysk zostanie przeniesione do `Transferring` stan, kiedy usługa zaczyna się na przesyłanie danych z dysku do usługi Windows Azure Storage.|
|`Completed`|Dysk zostanie przeniesione do `Completed` stanu, gdy usługa została pomyślnie przeniesiona wszystkie dane bez błędów.|
|`CompletedMoreInfo`|Dysk zostanie przeniesione do `CompletedMoreInfo` stanu, gdy Usługa napotkała problemy podczas kopiowania danych z lub na dysku. Informacje mogą zawierać błędy, ostrzeżenia lub komunikaty informacyjne o zastępowaniu obiektów blob.|
|`ShippedBack`|Dysk zostanie przeniesione do `ShippedBack` stan, gdy zostały wydane z tyłu centrum danych na adres zwrotny.|

W poniższej tabeli opisano stanów awarii dysku i akcje wykonywane dla każdego stanu.

|Stan stacji|Wydarzenie|Rozpoznawanie / następny krok|
|-----------------|-----------|-----------------------------|
|`NeverReceived`|Dysk, który jest oznaczony jako `NeverReceived` (ponieważ nie została odebrana jako część zadania wydania) dociera do innego wydania.|Zespół operacyjny przeniesie dysku `Received` stanu.|
|`N/A`|Dysk, który nie jest częścią dowolnego zadania dociera do centrum danych jako część innego zadania.|Dysk zostanie oznaczona jako dodatkowy dysk i zostanie zwrócony do klienta, po ukończeniu zadania skojarzone z oryginalnym pakietem.|

## <a name="faulted-states"></a>Stany uszkodzoną
Gdy zadania lub dysk nie powiedzie się normalnie postęp cyklu życia oczekiwane, zadania lub dysk zostanie przeniesiony do `Faulted` stanu. W tym momencie operacje zespół skontaktuje się z klienta, adres e-mail lub telefon. Po usunięciu problemu zadania uszkodzoną lub dysku zostaną wykonane poza `Faulted` stanu i przenoszony do właściwego stanu.

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
