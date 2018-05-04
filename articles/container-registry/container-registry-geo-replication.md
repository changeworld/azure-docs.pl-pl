---
title: Geograficznie replikowanie rejestru kontenera platformy Azure
description: Rozpocznij tworzenie i zarządzanie nimi rejestrów replikacją geograficzną kontenera platformy Azure.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 10/24/2017
ms.author: stevelas
ms.openlocfilehash: 6b82f49d2bf4ed321f5e847d11780535e01531f7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Wybierz firmy chcące obecność lokalnych lub gorących kopii zapasowej do uruchamiania usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem umieszczenie rejestru kontenera w każdym regionie, w którym są uruchamiane obrazy zezwala na operacje Zamknij sieci, umożliwiające szybkie, transfer warstwy niezawodnej obrazu.

Replikacja geograficzna umożliwia rejestru kontenera platformy Azure do działania jako pojedynczy rejestru, obsługę wielu regionach z wieloma serwerami głównymi regionalnych rejestrów.

> [!IMPORTANT]
> Funkcja replikacji geograficznej usługi Azure Container Registry jest aktualnie dostępna w **wersji zapoznawczej**. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.
>

Rejestr replikacją geograficzną zapewnia następujące korzyści:

* Jednej nazwy tagu rejestru/obrazu mogą być używane w różnych regionach
* Dostęp do sieci Zamknij rejestru od regionalnych wdrożeń
* Żadne opłaty dodatkowe wyjście jako obrazy są pobierane z lokalną, replikowanych rejestru w tym samym regionie co hosta kontenera
* Zarządzana rejestru w różnych regionach

## <a name="example-use-case"></a>Przykład przypadek użycia
Contoso uruchamia witryny sieci Web obecności publicznego umieszczone w Stanach Zjednoczonych, Kanady i Europie. Aby obsługiwać te rynkach z zawartości lokalnej, a następnie zamknij sieci, uruchamia Contoso [usługi kontenera platformy Azure](/azure/container-service/kubernetes/) Kubernetes (ACS) klastrów w zachodnie stany USA, wschodnie stany USA i Kanada centralnej oraz Europa Zachodnia. Witryny sieci Web aplikacji, wdrożonych jako obraz Docker korzysta z tego samego kodu i obrazów we wszystkich regionach. Zawartość lokalnego do tego regionu są pobierane z bazy danych, która zostanie zainicjowana jednoznacznie w każdym regionie. Dla każdego wdrożenia regionalnych ma konfigurację unikatowy dla zasobów, takich jak lokalnej bazy danych.

Zespół deweloperów znajduje się w Seattle, WA, wykorzystując zachodnie stany USA centrum danych.

![Wypychanie do wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Wypychanie do wielu rejestrów*

Przed użyciem funkcji replikacja geograficzna, Contoso ma rejestru amerykańskiej w zachodnie stany USA, z dodatkowych rejestru w Europa Zachodnia. Do obsługi tych różnych regionach, zespół deweloperów nastąpiło wypychanie obrazów do dwóch różnych rejestrów.

```bash
docker push contoso.azurecr.io/pubic/products/web:1.2
docker push contosowesteu.azurecr.io/pubic/products/web:1.2
```
![Ściąganie z wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ściąganie z wielu rejestrów*

Typowych wyzwań związanych z wielu rejestrów obejmują:

* Wschodnie stany USA, zachodnie stany USA i Kanada centralnej klastrów wszystkich ściąganie danych z rejestru zachodnie stany USA, są naliczane opłaty za wyjście, ponieważ każdy z tych hostów zdalnego kontenera ściąganie obrazów z centrów danych zachodnie stany USA.
* Zespół deweloperów musi Wypchnij obrazów do rejestrów zachodnie stany USA, Europa Zachodnia a.
* Zespół deweloperów należy skonfigurować i obsługa każdego wdrożenia regionalnych odwołujące się do lokalnego rejestru nazwy obrazu.
* Dostęp do rejestru należy skonfigurować dla każdego regionu.

## <a name="benefits-of-geo-replication"></a>Korzyści wynikające z replikacją geograficzną

![Ściąganie z rejestru z replikacją geograficzną](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Za pomocą funkcji replikacja geograficzna rejestru kontenera platformy Azure, są realizowane następujące korzyści:

* Zarządzanie jednym rejestru we wszystkich regionach: `contoso.azurecr.io`
* Zarządzanie pojedynczą konfiguracją wdrożenia obrazów, ponieważ wszystkie regiony używany ten sam adres URL obrazu: `contoso.azurecr.io/public/products/web:1.2`
* Wypychanie do pojedynczego rejestru, podczas gdy ACR zarządza replikację geograficzną, w tym regionalnych elementów webhook lokalnego powiadomienia o

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej
Konfigurowanie — replikacja geograficzna jest, wystarczy kliknąć regionów na mapie.

Replikacja geograficzna jest funkcją [rejestrów Premium](container-registry-skus.md) tylko. Jeśli rejestr nie jest jeszcze — wersja Premium, możesz zmienić w Basic i Standard do Premium w [portalu Azure](https://portal.azure.com):

![Przełączanie jednostki SKU w portalu Azure](media/container-registry-skus/update-registry-sku.png)

Aby skonfigurować georeplikacji — warstwa Premium rejestru, zaloguj się do portalu Azure pod adresem http://portal.azure.com.

Przejdź do rejestru kontenera Azure i wybierz **replikacje**:

![Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal](media/container-registry-geo-replication/registry-services.png)

Mapy wyświetlane są wyświetlane wszystkie bieżące regiony platformy Azure:

 ![Mapa regionów w witrynie Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Niebieski Sześciokąty reprezentują bieżącej repliki
* Zielony Sześciokąty reprezentują replik możliwych regionów
* Szara Sześciokąty reprezentują regiony platformy Azure, które nie są jeszcze dostępne dla replikacji

Aby skonfigurować replikę, wybierz sześciokąt zielony, a następnie wybierz **Utwórz**:

 ![Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal](media/container-registry-geo-replication/create-replication.png)

Aby skonfigurować dodatkowe replik, wybierz zielony Sześciokąty innych regionów, a następnie kliknij **Utwórz**.

ACR rozpocznie się Synchronizowanie obrazów w skonfigurowanym replik. Po wykonaniu tych czynności portalu odzwierciedla *gotowe*. Stan repliki w portalu nie jest automatycznie aktualizowany. Użyj przycisku odświeżania, aby wyświetlić zaktualizowany stan.

## <a name="geo-replication-pricing"></a>Replikacja geograficzna ceny

Replikacja geograficzna jest funkcją [warstwy Premium](container-registry-skus.md) rejestru kontenera platformy Azure. Podczas replikowania rejestru do sieci odpowiednią regionów, naliczane są opłaty rejestru Premium dla każdego regionu.

W powyższym przykładzie Contoso skonsolidowane dwóch rejestrów do jednego, Dodawanie repliki wschodnie stany USA, Kanada centralnej i Europa Zachodnia. Contoso może zwrócić Premium cztery razy na miesiąc bez dodatkowej konfiguracji i zarządzania. Każdy region ściąga teraz obrazów lokalnie, poprawa wydajności, niezawodności bez opłaty za wyjście sieci z zachodnie stany USA Kanady i wschodnie stany USA.

## <a name="summary"></a>Podsumowanie

Replikacja geograficzna umożliwiają zarządzanie Twojej regionalnych centrach danych jako jedną globalnego chmury. Obrazy są używane przez wiele usług platformy Azure, będzie można korzystać z płaszczyzny zarządzana przy zachowaniu sieci zamykania szybkiego i pobiera niezawodnej obrazu lokalnego.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z trzech części samouczka serii, [— replikacja geograficzna w rejestrze kontenera Azure](container-registry-tutorial-prepare-registry.md). Zapoznaj się z artykułem Tworzenie rejestru replikacją geograficzną, Tworzenie kontenera i wdrażania go za pomocą jednej `docker push` polecenia do wielu regionalne aplikacji sieci Web dla kontenery wystąpień.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w rejestrze kontenera platformy Azure](container-registry-tutorial-prepare-registry.md)