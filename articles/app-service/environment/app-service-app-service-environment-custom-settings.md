---
title: Niestandardowe ustawienia dla środowiska usługi App Service
description: Niestandardowych ustawień konfiguracji środowiska usługi App Service
services: app-service
documentationcenter: ''
author: stefsch
manager: nirma
editor: ''
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/22/2016
ms.author: stefsch
ms.custom: mvc
ms.openlocfilehash: d60cdca78c143996fa5935726db0631321c9e2fe
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2017
ms.locfileid: "26129519"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Niestandardowych ustawień konfiguracji środowiska usługi App Service
## <a name="overview"></a>Omówienie
Ponieważ środowiska usługi aplikacji są izolowane jednego odbiorcy, istnieją pewne ustawienia konfiguracji, które mogą być stosowane wyłącznie do środowiska usługi App Service. W tym artykule opisano różne specjalnego dostosowania, które są dostępne dla środowiska usługi App Service.

Jeśli nie masz środowisko usługi aplikacji, zobacz [tworzenie środowiska usługi aplikacji](app-service-web-how-to-create-an-app-service-environment.md).

Dostosowywanie środowiska usługi aplikacji może przechowywać przy użyciu tablicy w nowym **clusterSettings** atrybutu. Ten atrybut zostanie znaleziony w słowniku "Właściwości" *hostingEnvironments* jednostki usługi Azure Resource Manager.

Następujące skróconej Menedżera zasobów szablonu fragment kodu przedstawia **clusterSettings** atrybutu:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

**ClusterSettings** atrybut może być uwzględniany w szablonie usługi Resource Manager, aby zaktualizować środowiska usługi aplikacji.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Użyj Eksploratora zasobów Azure, aby zaktualizować środowiska usługi aplikacji
Alternatywnie można zaktualizować środowiska usługi aplikacji przy użyciu [Eksploratora zasobów Azure](https://resources.azure.com).  

1. W Eksploratorze zasobów, przejdź do węzła dla środowiska usługi aplikacji (**subskrypcje** > **resourceGroups** > **dostawców** > **Microsoft.Web** > **hostingEnvironments**). Następnie kliknij przycisk określonego środowiska usługi aplikacji, które chcesz zaktualizować.
2. W okienku po prawej stronie kliknij **odczytu/zapisu** w pasku narzędzi u góry umożliwia interakcyjne edycji w Eksploratorze zasobów.  
3. Polecenie **Edytuj** przycisk, aby dokonać edycji szablonu usługi Resource Manager.
4. Przewiń w dół w okienku po prawej stronie. **ClusterSettings** atrybut jest u dołu, gdzie można wprowadzić lub zaktualizować jej wartość.
5. Wpisz (lub skopiuj i Wklej) tablica wartości konfiguracji w **clusterSettings** atrybutu.  
6. Kliknij zielony **PUT** przycisku, który ma znajdujący się u góry w okienku po prawej stronie można przekazać zmian do środowiska usługi aplikacji.

Jednak wprowadzane zmiany trwa około 30 minut pomnożona przez liczbę front zakończeń w środowisku usługi aplikacji, aby zmiany zaczęły obowiązywać.
Na przykład jeśli środowiska usługi aplikacji ma cztery zakończenia frontonu, potrwa około dwie godziny na zakończenie aktualizacji konfiguracji. Gdy jest Trwa wprowadzanie zmian w konfiguracji, nie ma innych operacji skalowania lub operacje zmiany konfiguracji może mieć miejsce w środowisku usługi aplikacji.

## <a name="disable-tls-10"></a>Wyłączenie protokołu TLS 1.0
Cyklicznego zapytania od klientów, szczególnie w przypadku klientów, którzy mają do czynienia z zgodności PCI kontroli, jak jawnie wyłączenie protokołu TLS 1.0 dla swoich aplikacji.

Protokołu TLS 1.0, można wyłączyć za pomocą następujących **clusterSettings** wpis:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Zmień kolejność użycia mechanizmów szyfrowania TLS
Następne pytanie od klientów jest jeśli one zmodyfikuj listę szyfrów wynegocjowanym przez ich serwer i można to osiągnąć, modyfikując **clusterSettings** jak pokazano poniżej. Lista dostępnych mechanizmach szyfrowania można pobrać z [ten artykuł w witrynie MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Jeśli wartości są ustawiane dla mechanizmów szyfrowania, który SChannel nie może zrozumieć, całą komunikację TLS do serwera mogą przestać działać. W takim przypadku należy usunąć *FrontEndSSLCipherSuiteOrder* wpisu z **clusterSettings** i Prześlij zaktualizowany szablon usługi Resource Manager, aby powrócić do domyślnych ustawień pakietu szyfrowania.  Użyj tej funkcji należy z rozwagą.
> 
> 

## <a name="get-started"></a>Rozpoczęcie pracy
Witryna szablon Menedżera zasobów Azure szybkiego startu zawiera szablon z podstawowej definicji dla [tworzenie środowiska usługi aplikacji](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
