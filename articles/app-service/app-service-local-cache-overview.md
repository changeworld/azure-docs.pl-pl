---
title: Omówienie usługi Azure App Service lokalnej pamięci podręcznej | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób włączenia, zmienianie rozmiaru i zapytać o stan funkcji lokalnej pamięci podręcznej Azure App Service
services: app-service
documentationcenter: app-service
author: SyntaxC4
manager: yochayk
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
ms.author: cfowler
ms.openlocfilehash: 75f2dcb80514105ed663ba1fe5f7adccc05af1fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23836385"
---
# <a name="azure-app-service-local-cache-overview"></a>Omówienie usługi Azure App Service lokalnej pamięci podręcznej
Zawartość aplikacji sieci web platformy Azure są przechowywane w magazynie Azure i jest udostępniane w górę w sposób trwały jako udział zawartości. Ten projekt jest przeznaczony do pracy z różnych aplikacji i ma następujące atrybuty:  

* Zawartość jest współużytkowany przez wielu wystąpień maszyny wirtualnej (VM) w aplikacji sieci web.
* Zawartość jest trwałe i mogą być modyfikowane przez uruchamianie aplikacji sieci web.
* Plików dziennika i plików danych diagnostycznych są dostępne w tej samej folder zawartości udostępnionej.
* Publikowania nowej zawartości bezpośrednio aktualizuje folder zawartości. Natychmiast zobaczysz tej samej zawartości za pośrednictwem witryny sieci Web SCM i uruchomionej aplikacji sieci web (zwykle niektórych technologii, takich jak ASP.NET inicjować ponownego uruchomienia aplikacji sieci web na niektóre zmiany pliku, aby uzyskać najnowszą zawartość).

Podczas gdy wiele aplikacji sieci web co najmniej jedną z tych funkcji, niektóre aplikacje sieci web, wystarczy wysokiej wydajności, tylko do odczytu magazynu zawartości, które mogą uruchamiać z o wysokiej dostępności. Te aplikacje mogą korzystać z maszyny Wirtualnej wystąpienie określonego lokalnej pamięci podręcznej.

Funkcja lokalnej pamięci podręcznej Azure App Service zapewnia widok roli sieci web zawartości. Ta zawartość jest pamięć podręczną zapisu — ale odrzucenia zawartości magazynu, które utworzono uruchamiania asynchronicznie na miejscu. Gdy pamięć podręczna jest gotowy, lokacji jest włączane w celu uruchomienia zawartości w pamięci podręcznej. Aplikacje sieci Web, które działają w lokalnej pamięci podręcznej ma następujące zalety:

* Ich użycie jest odporna na opóźnienia, występujących podczas uzyskiwania dostępu do zawartości w magazynie Azure.
* Są one odporna do uaktualnienia planowane lub nieplanowane przestoje powodowane przez oraz innych zakłóceń z usługą Azure Storage, występujących na serwerach, które pełnią udziału zawartości.
* Mają one mniej uruchomieniu aplikacji z powodu zmiany udziału magazynu.

## <a name="how-local-cache-changes-the-behavior-of-app-service"></a>Jak lokalnej pamięci podręcznej zmiany zachowania usługi aplikacji
* Lokalnej pamięci podręcznej jest kopią folderów /site i /siteextensions aplikacji sieci web. Zostanie utworzony w lokalnym wystąpieniu maszyny Wirtualnej podczas uruchamiania aplikacji sieci web. Rozmiar lokalnej pamięci podręcznej dla aplikacji sieci web może zawierać maksymalnie 300 MB domyślnie, ale może zwiększyć maksymalnie 2 GB.
* Lokalnej pamięci podręcznej jest do odczytu / zapisu. Jednak wszelkie modyfikacje zostaną odrzucone po maszyny wirtualne są przenoszone lub pobiera ponownego uruchomienia aplikacji w sieci web. Nie używaj dla aplikacji przechowujących kluczowych danych w magazynie zawartości lokalnej pamięci podręcznej.
* Aplikacje sieci Web mogła nadal zapisywać pliki dzienników i danych diagnostycznych, tak jak obecnie. Pliki dzienników i danych, jednak są przechowywane lokalnie na maszynie Wirtualnej. Następnie one jest kopiowana za pośrednictwem okresowo magazynu zawartości udostępnionej. Kopiuj do magazynu zawartości udostępnionej jest najlepszym nakładu — zapisu, które tworzy kopię mogą zostać utracone powodu nagła awaria wystąpienia maszyny Wirtualnej.
* Brak zmian w strukturze folderu LogFiles i danych folderów dla aplikacji sieci web, które używają lokalnej pamięci podręcznej. Istnieją teraz podfoldery w magazynu danych i LogFiles foldery, w których wzorzec nazewnictwa "Unikatowy identyfikator" + sygnatury czasowej. Każdy podfolderów odpowiada wystąpienia maszyny Wirtualnej, w którym aplikacja sieci web jest uruchomiona lub jest uruchomione.  
* Publikowanie zmian w aplikacji sieci web przy użyciu dowolnego mechanizmu publikowania będą publikowane magazynu zawartości udostępnionej. To jest celowe, ponieważ chcemy opublikowana zawartość być trwałe. Aby odświeżyć aplikacji sieci web w lokalnej pamięci podręcznej, jego wymaga ponownego uruchomienia. To wydawać nadmiernego krok? Aby cyklem bezproblemowe, zapoznaj się z informacjami w dalszej części tego artykułu.
* D:\Home punktów do lokalnej pamięci podręcznej. D:\Local nadal wskaż magazyn tymczasowy specyficzne dla maszyny Wirtualnej.
* Domyślny widok zawartości witryny SCM jest nadal z magazynu zawartości udostępnionej.

## <a name="enable-local-cache-in-app-service"></a>Włącz lokalnej pamięci podręcznej w usłudze App Service
Konfigurowanie lokalnej pamięci podręcznej przy użyciu kombinacji ustawień aplikacji zastrzeżone. Te ustawienia aplikacji można skonfigurować przy użyciu następujących metod:

* [Witryna Azure Portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Skonfiguruj lokalnej pamięci podręcznej za pomocą portalu Azure
<a name="Configure-Local-Cache-Portal"></a>

Można włączyć lokalnej pamięci podręcznej na podstawie dla sieci web aplikacji za pomocą tego ustawienia aplikacji:`WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![Ustawienia aplikacji portalu Azure: lokalnej pamięci podręcznej](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Skonfiguruj lokalnej pamięci podręcznej za pomocą usługi Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```

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
Domyślnie rozmiar lokalnej pamięci podręcznej jest **300 MB**. W tym /site i /siteextensions folderów, które są kopiowane z magazynu zawartości, a także wszystkie lokalnie tworzone foldery dzienników i danych. Aby zwiększyć ten limit, użyj ustawienia aplikacji `WEBSITE_LOCAL_CACHE_SIZEINMB`. Można zwiększyć rozmiar do **2 GB** (2000 MB) dla aplikacji sieci web.

## <a name="best-practices-for-using-app-service-local-cache"></a>Najlepsze rozwiązania dotyczące korzystania z lokalnej pamięci podręcznej usługi aplikacji
Zalecane jest użycie lokalnej pamięci podręcznej w połączeniu z [środowiska przejściowe](../app-service/web-sites-staged-publishing.md) funkcji.

* Dodaj *trwałe* ustawienie aplikacji `WEBSITE_LOCAL_CACHE_OPTION` z wartością `Always` do Twojej **produkcji** miejsca. Jeśli używasz `WEBSITE_LOCAL_CACHE_SIZEINMB`, dodaj go jako trwałe ustawienie Twojego miejsca produkcji.
* Utwórz **przemieszczania** gniazdo i publikowanie do Twojego miejsca przemieszczania. Zazwyczaj nie zostanie ustawiona miejsce wystawiania, użyj lokalnej pamięci podręcznej, aby włączyć bezproblemowe cykl kompilacja wdrażanie testy do przemieszczania, jeśli otrzymasz korzyści wynikające z lokalnej pamięci podręcznej do miejsca produkcji.
* Testowanie witryny Twojego miejsca przemieszczania.  
* Gdy wszystko będzie gotowe do wydania [operacji wymiana](../app-service/web-sites-staged-publishing.md#Swap) między przemieszczania i produkcji gniazd.  
* Trwałe ustawienia obejmują nazwę i jego umocowania do gniazda. Dlatego miejsca przemieszczania pobiera miejscami do środowiska produkcyjnego, dziedziczy ustawienia aplikacji lokalnej pamięci podręcznej. Nowo zamieniono miejsca produkcji będzie uruchamiana w lokalnej pamięci podręcznej po kilku minutach i będzie można przygotowaniu miejsca jako część rozgrzewania miejsca po wymiany. Po zakończeniu wymiany gniazd, Twojego miejsca produkcji jest uruchomiony w lokalnej pamięci podręcznej.

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania
### <a name="how-can-i-tell-if-local-cache-applies-to-my-web-app"></a>Jak sprawdzić, czy lokalnej pamięci podręcznej ma zastosowanie do mojej aplikacji sieci web?
Jeśli aplikacja sieci web powinien niezwykle wydajny i niezawodny magazyn zawartości, nie używaj magazynu zawartości do zapisywania danych krytycznych w czasie wykonywania i całkowity rozmiar jest mniejszy niż 2 GB, następnie odpowiedź to "yes"! Aby uzyskać całkowity rozmiar folderów /site i /siteextensions, można użyć rozszerzenia lokacji "Azure Web Apps użycie dysku."

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Jak sprawdzić, czy Moja witryna została przełączona przy użyciu lokalnej pamięci podręcznej?
Jeśli używasz funkcji lokalnej pamięci podręcznej z środowiska przejściowe, operacja zamiany nie ukończenia lokalnej pamięci podręcznej jest przygotowaniu miejsca. Aby sprawdzić, czy witryna działa z lokalnej pamięci podręcznej, można sprawdzić zmiennej środowiskowej proces roboczy `WEBSITE_LOCALCACHE_READY`. Postępuj zgodnie z instrukcjami na [zmiennej środowiskowej proces roboczy](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) stronę, aby uzyskać dostępu do zmiennej środowiskowej procesu roboczego na wiele wystąpień.  

### <a name="i-just-published-new-changes-but-my-web-app-does-not-seem-to-have-them-why"></a>Po opublikowaniu tylko nowych zmian, ale aplikacja sieci web nie wydaje się do nich. Dlaczego?
Jeśli aplikacja sieci web korzysta z lokalnej pamięci podręcznej, należy ponownie uruchomić witryny, aby pobrać najnowsze zmiany. Nie chcesz opublikować zmian do miejsca produkcji? Zobacz Opcje miejsca w poprzedniej sekcji najlepszych rozwiązań.

### <a name="where-are-my-logs"></a>Gdzie znajdują się Moje dzienniki?
Z lokalnej pamięci podręcznej folderów danych i dzienniki wyglądać nieco inaczej. Jednak struktury sieci podfoldery jest taka sama, z wyjątkiem tego, czy podfoldery są nestled w obszarze podfolder o format "Unikatowy identyfikatora maszyny Wirtualnej" + sygnatury czasowej.

### <a name="i-have-local-cache-enabled-but-my-web-app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Mam włączoną lokalnej pamięci podręcznej, ale aplikacja sieci web nadal pobiera uruchomiony ponownie. Dlaczego jest to, że? Myślę, że pomogła lokalnej pamięci podręcznej z częste aplikacji zostanie ponownie uruchomiony.
Lokalnej pamięci podręcznej pomagać w zapobieganiu ponownego uruchomienia aplikacji dotyczące magazynu w sieci web. Jednak aplikacja sieci web można nadal przechodzą ponownego uruchomienia podczas uaktualniania infrastruktury planowanej maszyny wirtualnej. Powinien być mniej ogólny ponowne uruchomienia aplikacji, które występują z lokalnej pamięci podręcznej włączone.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Lokalnej pamięci podręcznej wyklucza wszystkie katalogi zapobiega kopiowaniu szybciej dysku?
W ramach kroku, który kopiuje zawartość magazynu folderu o nazwie repozytorium jest wyłączone. Pomaga to w scenariuszach, w którym zawartości witryny może zawierać repozytorium kontroli źródła, który nie może być wymagany w codziennych operacji w aplikacji sieci web. 
