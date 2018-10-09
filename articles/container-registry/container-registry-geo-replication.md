---
title: Rejestr Azure container registry replikacji geograficznej
description: Rozpocznij tworzenie i zarządzanie nimi rejestry kontenerów platformy Azure z replikacją geograficzną.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview-article
ms.date: 04/10/2018
ms.author: stevelas
ms.openlocfilehash: 784174c1fb2427441e0ed1a13b147d2440539fa9
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870342"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Wybierz firmy chcące obecność na rynku lokalnym lub gorąca kopii zapasowej do uruchamiania usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem jest umieszczenie rejestr kontenera w każdym regionie, w którym są uruchamiane obrazów umożliwia wykonywanie operacji sieci w pobliżu, umożliwiające szybkie, transfer warstwy niezawodne obrazu. Replikacja geograficzna umożliwia rejestr Azure container registry działa jak pojedynczy rejestr obsługująca wielu regionów za pomocą wielu wzorców, regionalnych rejestrów.

Rejestr z replikacją geograficzną zapewnia następujące korzyści:

* Jednej nazwy tagu/obrazu/rejestru mogą być używane w wielu regionach
* Dostęp do rejestru pobliskiej sieci od wdrożeń regionalnych
* Nie trzeba ponosić opłat dodatkowy ruch wychodzący jako obrazy są pobierane z lokalnego, replikowany rejestru w tym samym regionie, co hosta kontenera
* To samo Zarządzanie rejestru w wielu regionach

## <a name="example-use-case"></a>Przykład przypadek użycia
Contoso uruchamia obecności publicznej witryny sieci Web znajdujących się w różnych Stanach Zjednoczonych, Kanadzie i Europie. Aby obsługiwać te rynki z zawartości lokalnej i sieci w pobliżu, uruchamia Contoso [usługi Azure Container Service](/azure/container-service/kubernetes/) klastrów Kubernetes (ACS), w regionie zachodnie stany USA, wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Aplikację witryny sieci Web, wdrażane jako obraz platformy Docker, korzysta z tego samego kodu i obrazów we wszystkich regionach. Zawartość lokalnego do tego regionu są pobierane z bazy danych, która jest obsługiwana w sposób unikatowy w każdym regionie. Każde wdrożenie regionalnych ma konfigurację unikatowy dla zasobów, takich jak lokalnej bazy danych.

Zespół deweloperów znajduje się w Seattle, WA, przy użyciu Centrum danych w regionie zachodnie stany USA.

![Wypychanie do wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Wypychanie do wielu rejestrów*

Przed użyciem funkcji replikacji geograficznej, firma Contoso ma rejestru amerykańskiej w regionie zachodnie stany USA, za pomocą dodatkowych rejestru w regionie Europa Zachodnia. Aby obsługiwać te różnych regionach, zespół miał wypchnąć obrazy do dwóch różnych rejestrów.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Pobieranie z wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Pobieranie z wielu rejestrów*

Typowe problemy z wielu rejestrów obejmują:

* Klastry wschodnie stany USA, zachodnie stany USA i Kanada Środkowa ściąganie z rejestru zachodnie stany USA, naliczania opłat za ruch wychodzący zgodnie z każdego z tych hostów w zdalnym kontenerze ściągania obrazów z centrów danych w regionie zachodnie stany USA.
* Zespół programistyczny musi wypychanie obrazów do rejestrów zachodnie stany USA i Europa Zachodnia.
* Zespół programistyczny musi konfigurowania i konserwacji każdego regionalne wdrożenia przy użyciu nazwy obrazów odwołujące się do rejestru lokalnego.
* Dostęp do rejestru musi być skonfigurowany dla każdego regionu.

## <a name="benefits-of-geo-replication"></a>Korzyści z replikacją geograficzną

![Ściąganie z rejestru z replikacją geograficzną](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Przy użyciu funkcji replikacji geograficznej usługi Azure Container Registry, są realizowane następujące korzyści:

* Zarządzanie pojedynczym rejestrem we wszystkich regionach: `contoso.azurecr.io`
* Zarządzanie jedną konfigurację wdrożenia obrazu, ponieważ we wszystkich regionach jest używany ten sam adres URL obrazu: `contoso.azurecr.io/public/products/web:1.2`
* Wypychanie do pojedynczym rejestrem, podczas gdy, rejestru Azure container Registry zarządza replikację geograficzną, w tym regionalne elementy webhook dla powiadomień lokalnych

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej
Konfigurowanie replikacji geograficznej jest równie proste jak klikając regiony na mapie.

Replikacja geograficzna jest funkcją [rejestrach w warstwie Premium](container-registry-skus.md) tylko. Jeśli rejestru nie jest jeszcze — wersja Premium, możesz zmienić podstawowa i standardowa do warstwy Premium w [witryny Azure portal](https://portal.azure.com):

![Przełączanie jednostek SKU w witrynie Azure portal](media/container-registry-skus/update-registry-sku.png)

Aby skonfigurować replikację geograficzną dla rejestr w warstwie Premium, zaloguj się do witryny Azure portal pod http://portal.azure.com.

Przejdź do usługi Azure Container Registry, a następnie wybierz pozycję **replikacje**:

![Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zostanie wyświetlona mapa przedstawiająca wszystkie bieżące regionów platformy Azure:

 ![Mapa regionów w witrynie Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Sześciokąty niebieskiego reprezentują bieżącej repliki
* Zielony Sześciokąty reprezentują możliwe repliki regionów
* Szara Sześciokąty reprezentują regiony platformy Azure nie jest jeszcze dostępna dla replikacji

Aby skonfigurować replikę, wybierz zielony sześciokąt, a następnie wybierz **Utwórz**:

 ![Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal](media/container-registry-geo-replication/create-replication.png)

Aby skonfigurować dodatkowe repliki, zaznacz zielony Sześciokąty innych regionów, a następnie kliknij **Utwórz**.

Rejestru Azure container Registry rozpoczyna synchronizowanie obrazów między replikami skonfigurowany. Po wykonaniu tych czynności, portalu będzie wyświetlany stan *gotowe*. Stan repliki w portalu nie są automatycznie aktualizowane. Użyj przycisku Odśwież, aby zobaczyć zaktualizowany stan.

## <a name="geo-replication-pricing"></a>Cennik replikacji geograficznej

Replikacja geograficzna jest funkcją [jednostki SKU Premium](container-registry-skus.md) usługi Azure Container Registry. W przypadku replikacji rejestru regionom żądaną wiąże się z wersji Premium opłaty rejestru dla każdego regionu.

W poprzednim przykładzie Contoso skonsolidowane dwóch rejestrów do jednego, dodawanie replik wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Contoso płaci Premium cztery razy na miesiąc, bez dodatkowej konfiguracji lub zarządzania. Każdy region ściąga teraz obrazów lokalnie, zwiększania wydajności i niezawodności bez opłatami za dane wychodzące w sieci z regionu zachodnie stany USA do Kanady i wschodnie stany USA.

## <a name="summary"></a>Podsumowanie

Dzięki replikacji geograficznej można zarządzać swoje regionalnych centrach danych jako jedna chmura globalnego. Obrazy są używane w wielu usługach platformy Azure, mogą korzystać z pojedynczej powierzchni zarządzania, przy zachowaniu sieci w pobliżu, szybkie i niezawodne lokalny obraz ściąga.

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z częścią trzyczęściowej serii samouczków [replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md). Opisano tworzenie rejestru z replikacją geograficzną, utworzenie kontenera i wdrażania go za pomocą jednego `docker push` polecenia do wielu aplikacji sieci Web regionalnych wystąpień kontenerów.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md)
