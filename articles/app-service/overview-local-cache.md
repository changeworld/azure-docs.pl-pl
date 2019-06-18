---
title: Omówienie lokalnej pamięci podręcznej — usłudze Azure App Service | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączyć, zmienianie rozmiaru i wykonać zapytanie o stan funkcji lokalnej pamięci podręcznej usługi Azure App Service
services: app-service
documentationcenter: app-service
author: cephalin
manager: jpconnock
editor: ''
tags: optional
keywords: ''
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1d6e233509b50f0b03678f2e62267169d02133a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60839052"
---
# <a name="azure-app-service-local-cache-overview"></a>Omówienie lokalnej pamięci podręcznej App Service na platformie Azure

> [!NOTE]
> Lokalna pamięć podręczna nie jest obsługiwane w aplikacji funkcji lub konteneryzowanych aplikacji usługi App Service, takie jak na [usługi App Service w systemie Linux](containers/app-service-linux-intro.md).


Zawartość usługi Azure App Service są przechowywane w usłudze Azure Storage i jest udostępniane w górę w sposób trwały jako udział zawartości. Ten projekt jest przeznaczony do pracy w różnych aplikacjach i ma następujące atrybuty:  

* Zawartość jest udostępniana w wielu wystąpieniach maszyny wirtualnej (VM) w aplikacji.
* Zawartość jest trwałe i mogą być modyfikowane przez uruchamianie aplikacji.
* Plików dziennika i plików danych diagnostycznych są dostępne w ramach tego samego folderu zawartości udostępnionej.
* Publikowanie nowej zawartości bezpośrednio aktualizuje folder zawartości. Natychmiast zobaczysz tę samą zawartość, za pośrednictwem witryny sieci Web funkcji SCM i jej uruchamianie aplikacji (zwykle niektóre technologie, takie jak ASP.NET zainicjować ponowne uruchomienie aplikacji na pewne zmiany pliku, aby pobrać najnowszą zawartość).

Podczas, gdy wiele aplikacji użyj co najmniej jedną z tych funkcji, niektóre aplikacje wymagają tylko niezwykle wydajny i tylko do odczytu magazynu zawartości, którą można uruchomić z o wysokiej dostępności. Te aplikacje mogą korzystać z wystąpienia maszyny Wirtualnej określonej lokalnej pamięci podręcznej.

Funkcja lokalna pamięć podręczna Azure App Service udostępnia widok roli sieci web zawartości. Ta zawartość jest pamięć podręczna zapisu — ale odrzucenia zawartości magazynu, które jest tworzony na uruchamianie asynchronicznie na miejscu. Gdy pamięć podręczna jest gotowy, witryna zostanie przełączone na uruchamiać zawartości w pamięci podręcznej. Aplikacje, które działają w lokalnej pamięci podręcznej zapewnia następujące korzyści:

* Są one odporny na opóźnienia, które występują podczas uzyskiwania dostępu do zawartości w usłudze Azure Storage.
* Są one odporny na uaktualnienia planowane lub nieplanowane przestoje oraz innych zakłóceń z usługą Azure Storage, występujących na serwery, które obsługują udziału zawartości.
* Mają one mniej aplikacji ponownego uruchomienia ze względu na zmiany udziału magazynu.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Jak lokalna pamięć podręczna zmienia działanie usługi App Service
* _D:\home_ wskazuje lokalnej pamięci podręcznej, która jest tworzona na wystąpieniu maszyny Wirtualnej, podczas uruchamiania aplikacji. _D:\Local_ wskaż magazynu tymczasowego specyficznych dla maszyny Wirtualnej w dalszym ciągu.
* Lokalna pamięć podręczna zawiera jednorazową kopię _/lokacji_ i _/siteextensions_ folderów magazynu zawartości udostępnionej w _D:\home\site_ i _D:\home\ siteextensions_, odpowiednio. Pliki są kopiowane do lokalnej pamięci podręcznej podczas uruchamiania aplikacji. Rozmiar dwa foldery dla każdej aplikacji jest ograniczona do 300 MB domyślnie, ale można go zwiększyć do 2 GB.
* Lokalna pamięć podręczna jest do odczytu i zapisu. Jednak wszelkie zmiany zostaną odrzucone po aplikacji do przenoszenia maszyn wirtualnych lub pobiera ponownego uruchomienia. Nie należy używać lokalnej pamięci podręcznej dla aplikacji, które przechowują dane o kluczowym znaczeniu w magazynie zawartości.
* _D:\home\LogFiles_ i _D:\home\Data_ zawiera pliki dziennika i danych aplikacji. Dwa podfoldery są przechowywane lokalnie w wystąpieniu maszyny Wirtualnej i są kopiowane do magazynu zawartości udostępnionej okresowo. Aplikacje można utrwalić plików dziennika i danych, zapisując je do tych folderów. Kopiuj do magazynu zawartości udostępnionej jest jednak staranności, więc istnieje możliwość, plików dziennika i danych zostanie utracone z powodu awarii nagłe wystąpienia maszyny Wirtualnej.
* [Przesyłanie strumieniowe dzienników](troubleshoot-diagnostic-logs.md#streamlogs) jest zależna od kopiowania największej staranności. Można obserwować, maksymalnie jednej minucie w dziennikach przesyłane strumieniowo.
* W przypadku magazynu zawartości udostępnionej powoduje zmianę struktury folderów _LogFiles_ i _danych_ folderów dla aplikacji korzystających z lokalnej pamięci podręcznej. Dostępne są teraz podfoldery w nich, które mają wzorzec nazewnictwa "Unikatowy identyfikator" + sygnaturę czasową. Wszystkich podfolderów odnosi się do wystąpienia maszyny Wirtualnej, w którym aplikacja jest uruchomiona lub zostało uruchomione.
* Innych folderów w _D:\home_ pozostają w lokalnej pamięci podręcznej i nie są kopiowane do magazynu zawartości udostępnionej.
* Wdrażanie aplikacji przy użyciu dowolnej obsługiwanej metody publikuje bezpośrednio do trwałego magazynu zawartości udostępnionej. Aby odświeżyć _D:\home\site_ i _D:\home\siteextensions_ folderów w lokalnej pamięci podręcznej, aplikacja musi zostać ponownie uruchomione. Aby cyklu życia bezproblemowe, zobacz informacje w dalszej części tego artykułu.
* Domyślny widok zawartości witryny SCM jest nadal w przypadku magazynu zawartości udostępnionej.

## <a name="enable-local-cache-in-app-service"></a>Włącz lokalną pamięć podręczną w usłudze App Service
Można skonfigurować lokalną pamięć podręczną za pomocą kombinacji ustawień zastrzeżonych aplikacji. Te ustawienia aplikacji można skonfigurować przy użyciu następujących metod:

* [Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Skonfigurować lokalną pamięć podręczną za pomocą witryny Azure portal
<a name="Configure-Local-Cache-Portal"></a>

Włącz lokalną pamięć podręczną na podstawie poszczególnych web-app odbywa się przy użyciu tego ustawienia aplikacji: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Ustawienia aplikacji z portalu Azure: Lokalna pamięć podręczna](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Skonfigurować lokalną pamięć podręczną za pomocą usługi Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "300"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Zmień ustawienie rozmiaru w lokalnej pamięci podręcznej
Domyślnie rozmiar lokalnej pamięci podręcznej jest **300 MB**. W tym /site i /siteextensions folderów, które są kopiowane z magazynu zawartości, jak również wszelkie lokalnie tworzone foldery dzienników i danych. Aby zwiększyć ten limit, użyj ustawienia aplikacji `WEBSITE_LOCAL_CACHE_SIZEINMB`. Maksymalnie zwiększyć rozmiar **2 GB** (2000 MB) dla aplikacji.

## <a name="best-practices-for-using-app-service-local-cache"></a>Najlepsze rozwiązania dotyczące korzystania z lokalnej pamięci podręcznej usługi aplikacji
Zalecamy użycie lokalnej pamięci podręcznej w połączeniu z [środowisk przejściowych](../app-service/deploy-staging-slots.md) funkcji.

* Dodaj *umocowany* ustawienia aplikacji `WEBSITE_LOCAL_CACHE_OPTION` wartością `Always` do Twojej **produkcji** miejsca. Jeśli używasz `WEBSITE_LOCAL_CACHE_SIZEINMB`, także dodać go jako atrybut sticky ustawienie z miejscem produkcyjnym.
* Tworzenie **przemieszczania** gniazdo i publikować swoje miejsce przejściowe. Zazwyczaj nie ustawisz miejsca przejściowego do użycia lokalnej pamięci podręcznej umożliwia bezproblemowe cyklu życia kompilacja wdrażanie testy dla przejściowym, jeśli możesz uzyskać korzyści wynikające z lokalnej pamięci podręcznej z miejscem produkcyjnym.
* Przetestuj swoją witrynę przed swoje miejsce przejściowe.  
* Gdy wszystko będzie gotowe, należy wydać [Operacja zamiany miejsc](../app-service/deploy-staging-slots.md#Swap) między przejściowe i produkcyjne miejsca.  
* Umocowany ustawienia obejmują nazwę oraz umocowany do gniazda. Dlatego miejsce przejściowe pobiera zamienione w środowisku produkcyjnym, dziedziczy ustawienia aplikacji lokalnej pamięci podręcznej. Nowo zamieniono miejsca produkcji zostanie wykonane względem lokalnej pamięci podręcznej po kilku minutach, a będzie można przygotowaniu jako część rozgrzewania miejsca po wymiany. Po zakończeniu zamiany miejsca, z miejscem produkcyjnym jest uruchomiony w lokalnej pamięci podręcznej.

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania
### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Jak sprawdzić, jeśli lokalna pamięć podręczna ma zastosowanie do mojej aplikacji?
Jeśli Twoja aplikacja potrzebuje wydajny i niezawodny magazyn zawartości, nie za pomocą magazynu zawartości do zapisu krytycznych danych w czasie wykonywania i jest mniejszy niż 2 GB w łącznym rozmiarze, następnie odpowiedź to "yes"! Aby uzyskać całkowity rozmiar folderów /site i /siteextensions, można użyć rozszerzenia witryny "Azure Web Apps użycie dysku."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak sprawdzić, jeśli Moja witryna została przełączona do korzystania z lokalnej pamięci podręcznej?
Jeśli używasz funkcji lokalnej pamięci podręcznej przy użyciu środowiska przejściowe, operacja zamiany nie zostanie ukończone, aż lokalnej pamięci podręcznej jest przygotowaniu. Aby sprawdzić, czy witryna działa względem lokalnej pamięci podręcznej, można sprawdzić zmiennej środowiskowej procesu roboczego `WEBSITE_LOCALCACHE_READY`. Postępuj zgodnie z instrukcjami na [zmiennej środowiskowej procesu roboczego](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) strony, aby dostęp do zmiennej środowiskowej procesu roboczego w wielu wystąpieniach.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Czy mogę po prostu publikowania nowych zmian, ale Moja aplikacja nie wydaje się, że ich. Dlaczego?
Jeśli aplikacja korzysta z lokalnej pamięci podręcznej, należy ponownie uruchomić witryny, aby pobrać najnowsze zmiany. Nie chcesz opublikować zmiany do miejsca produkcji? Zobacz Opcje miejsca w poprzedniej sekcji najlepszych praktyk.

### <a name="where-are-my-logs"></a>Gdzie są Moje dzienniki?
Z lokalnej pamięci podręcznej folderów danych i dzienników wyglądają nieco inaczej. Jednak struktury z podfolderów pozostaje taki sam, z tą różnicą, że podfoldery są nestled w podfolderze przy użyciu formatu "Unikatowy maszynę Wirtualną identyfikator" i sygnaturę czasową.

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Lokalna pamięć podręczna, włączone na komputerze, ale Moja aplikacja nadal pobiera ponownego uruchomienia. Dlaczego jest to, że? Wydawało się, że brały udział w lokalnej pamięci podręcznej za pomocą aplikacji częstego ponownego uruchomienia.
Lokalna pamięć podręczna uniknąć ponownych uruchomień aplikacji dotyczące magazynu. Jednak aplikacja nadal można przechodzić ponownego uruchomienia podczas uaktualniania zaplanowanej infrastruktury maszyny wirtualnej. Ogólny ponowne uruchomienia aplikacji, które z lokalnej pamięci podręcznej włączone powinno być mniej.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Lokalna pamięć podręczna wyklucza wszystkie katalogi kopiowaniu szybciej dysku lokalnym?
W ramach tego kroku, który kopiuje zawartość magazynu dowolny folder, który nosi nazwę repozytorium jest wykluczona. Pomaga to w scenariuszach, gdzie zawartości witryny może zawierać repozytorium kontroli źródła, które nie mogą być potrzebne w codzienne działania aplikacji. 
