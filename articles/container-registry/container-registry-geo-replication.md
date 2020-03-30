---
title: Replikacja geograficzna rejestru
description: Rozpocznij tworzenie i zarządzanie geograficznie replikowanym rejestrem kontenerów platformy Azure, który umożliwia rejestrowi obsługiwanie wielu regionów za pomocą replik regionalnych z wieloma wzorcami.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456445"
---
# <a name="geo-replication-in-azure-container-registry"></a>Replikacja geograficzna w usłudze Azure Container Registry

Firmy, które chcą zaznaczyć swoją obecność lokalną lub wykonywać dynamiczną kopię zapasową, decydują się na uruchamianie usług w wielu regionach platformy Azure. Najlepszym rozwiązaniem jest umieszczenie rejestru kontenerów w każdym regionie, w którym uruchamiane są obrazy. Umożliwia to wykonywanie operacji w pobliskiej sieci, dzięki czemu transfery w warstwie obrazu są szybkie i niezawodne. Replikacja geograficzna umożliwia działanie rejestru kontenerów platformy Azure jako pojedynczego rejestru obsługującego wiele regionów przy użyciu wielowzorcowych rejestrów regionalnych. 

Rejestr z replikacją geograficzną zapewnia następujące korzyści:

* Możliwość używania pojedynczych nazw rejestru/obrazu/tagu w wielu regionach
* Dostęp do rejestru z pobliskiej sieci z poziomu wdrożeń regionalnych
* Brak dodatkowych opłat za ruch wychodzący — obrazy są pobierane z lokalnego, zreplikowanego rejestru znajdującego się w tym samym regionie co host kontenera
* Pojedyncza usługa zarządzania rejestrem w wielu regionach

> [!NOTE]
> Jeśli zachodzi potrzeba obsługi kopii obrazów kontenerów w więcej niż jednym rejestrze kontenerów platformy Azure, usługa Azure Container Registry obsługuje również [importowanie obrazów](container-registry-import-images.md). Na przykład w ramach przepływu pracy DevOps można zaimportować obraz z rejestru deweloperskiego do rejestru produkcyjnego bez konieczności używania poleceń platformy Docker.
>

## <a name="example-use-case"></a>Przykładowy przypadek użycia
Firma Contoso posiada dostępną publicznie witryny internetową zlokalizowaną w Stanach Zjednoczonych, Kanadzie i Europie. Aby obsługiwać te rynki przy użyciu zawartości lokalnej dostępnej w pobliskiej sieci, firma Contoso uruchamia klastry usługi [Azure Kubernetes Service](/azure/aks/) (AKS) w regionach Zachodnie stany USA, Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Aplikacja internetowa wdrożona jako obraz platformy Docker korzysta z tego samego kodu i obrazu we wszystkich regionach. Zawartość, lokalna w danym regionie, jest pobierana z bazy danych aprowizowanej w sposób unikatowy w każdym regionie. Dla każdego wdrożenia regionalnego istnieje unikatowa konfiguracja zasobów, takich jak lokalna baza danych.

Zespół programistyczny znajduje się w Seattle w stanie Waszyngton i korzysta z centrum danych w regionie Zachodnie stany USA.

![Wypychanie do wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Wypychanie do wielu rejestrów*

Zanim były używane funkcje replikacji geograficznej, firma Contoso posiadała rejestr znajdujący się w regionie Zachodnie stany USA oraz dodatkowy rejestr w regionie Europa Zachodnia. W celu obsługi tych regionów zespół programistyczny wypychał obrazy do dwóch różnych rejestrów.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Ściąganie z wielu rejestrów](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ściąganie z wielu rejestrów*

Typowe wyzwania związane z korzystaniem z wielu rejestrów:

* Klastry w regionach Wschodnie stany USA, Zachodnie stany USA i Kanada Środkowa są ściągane z rejestru w regionie Zachodnie stany USA, co powoduje naliczanie opłat za ruch wychodzący, ponieważ każdy z tych zdalnych hostów kontenerów ściąga obrazy z centrów danych w regionie Zachodnie stany USA.
* Zespół programistyczny musi wypychać obrazy do rejestrów w regionach Zachodnie stany USA i Europa Zachodnia.
* Zespół programistyczny musi konfigurować i konserwować każde wdrożenie regionalne przy użyciu nazw obrazów odwołujących się do rejestru lokalnego.
* Dostęp do rejestru musi być skonfigurowany dla każdego regionu.

## <a name="benefits-of-geo-replication"></a>Korzyści z replikacji geograficznej

![Ściąganie z rejestru z replikacją geograficzną](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Funkcja replikacji geograficznej usługi Azure Container Registry zapewnia następujące korzyści:

* Zarządzanie pojedynczym rejestrem w wielu regionach: `contoso.azurecr.io`
* Zarządzanie jedną konfiguracją wdrożeń obrazów, ponieważ we wszystkich regionach jest używany ten sam adres URL obrazu: `contoso.azurecr.io/public/products/web:1.2`
* Wypychanie do jednego rejestru, podczas gdy usługa ACR zarządza replikacją geograficzną. Regionalne [elementy webhook](container-registry-webhook.md) można skonfigurować w celu powiadamiania o zdarzeniach w określonych replikach.

## <a name="configure-geo-replication"></a>Konfigurowanie replikacji geograficznej

Konfigurowanie replikacji geograficznej jest równie proste, co klikanie regionów na mapie. Replikację geograficzną można również zarządzać za pomocą narzędzi, w tym poleceń [replikacji az acr](/cli/azure/acr/replication) w narzędziu wiersza polecenia platformy Azure, lub wdrożyć rejestr włączony do replikacji geograficznej za pomocą [szablonu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Replikacja geograficzna jest funkcją dotyczącą tylko [rejestrów w warstwie Premium](container-registry-skus.md). Jeśli Twój rejestr nie znajduje się jeszcze w warstwie Premium, możesz zmienić warstwę z warstwy Podstawowa i Standardowa na warstwę Premium w [witrynie Azure Portal](https://portal.azure.com):

![Przełączanie jednostek SKU w witrynie Azure Portal](media/container-registry-skus/update-registry-sku.png)

Aby skonfigurować replikację geograficzną dla rejestru w warstwie Premium, zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

Przejdź do używanego rejestru Azure Container Registry, a następnie wybierz pozycję **Replikacje**:

![Replikacje w interfejsie użytkownika rejestru kontenerów w witrynie Azure Portal](media/container-registry-geo-replication/registry-services.png)

Zostanie wyświetlona mapa przedstawiająca wszystkie bieżące regiony platformy Azure:

 ![Mapa regionów w witrynie Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Niebieskie sześciokąty reprezentują bieżące repliki
* Zielone sześciokąty reprezentują możliwe regiony replik
* Szare sześciokąty reprezentują regiony platformy Azure, które nie są jeszcze dostępne dla replikacji

Aby skonfigurować replikę, wybierz zielony sześciokąt, a następnie wybierz pozycję **Utwórz**:

 ![Tworzenie interfejsu użytkownika replikacji w witrynie Azure Portal](media/container-registry-geo-replication/create-replication.png)

Aby skonfigurować dodatkowe repliki, wybierz zielone sześciokąty dla innych regionów, a następnie kliknij pozycję **Utwórz**.

Usługa ACR rozpocznie synchronizowanie obrazów między skonfigurowanymi replikami. Po ukończeniu tego zadania w portalu zostanie wyświetlony stan *Gotowe*. Stan repliki w portalu nie jest automatycznie aktualizowany. Użyj przycisku odświeżania, aby wyświetlić zaktualizowany stan.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Zagadnienia dotyczące korzystania z rejestru replikowanego geograficznie

* Każdy region w rejestrze replikowanym geograficznie jest niezależny po skonfigurowaniu. Łas sla rejestru kontenerów platformy Azure mają zastosowanie do każdego regionu replikowanego geograficznie.
* Podczas wypychania lub ściągania obrazów z rejestru replikowanego geograficznie usługa Azure Traffic Manager w tle wysyła żądanie do rejestru znajdującego się w regionie znajdującym się najbliżej Ciebie.
* Po wypchnięciu aktualizacji obrazu lub tagu do najbliższego regionu, zajmuje trochę czasu dla usługi Azure Container Registry do replikacji manifestów i warstw do pozostałych regionów, które zostały włączone. Replikowanie większych obrazów trwa dłużej niż mniejsze. Obrazy i znaczniki są synchronizowane w różnych regionach replikacji z modelu spójności ostatecznej.
* Aby zarządzać przepływami pracy, które zależą od aktualizacji wypychania do replikowanego geograficznie, zaleca się skonfigurowanie [elementów webhook](container-registry-webhook.md) w celu reagowania na zdarzenia wypychania. Regionalne elementy webhook można skonfigurować w rejestrze replikowanym geograficznie, aby śledzić zdarzenia wypychania w miarę ich wypełniania w regionach replikowanych geograficznie.

## <a name="delete-a-replica"></a>Usuwanie repliki

Po skonfigurowaniu repliki dla rejestru można ją usunąć w dowolnym momencie, jeśli nie jest już potrzebna. Usuń replikę przy użyciu portalu Azure lub innych narzędzi, takich jak polecenie [usuwania replikacji az acr](/cli/azure/acr/replication#az-acr-replication-delete) w narzędziu wiersza polecenia platformy Azure CLI.

Aby usunąć replikę w witrynie Azure portal:

1. Przejdź do rejestru kontenerów platformy Azure i wybierz pozycję **Replikacje**.
1. Wybierz nazwę repliki i wybierz pozycję **Usuń**. Upewnij się, że chcesz usunąć replikę.

> [!NOTE]
> Nie można usunąć repliki rejestru w *regionie macierzystym* rejestru, czyli lokalizacji, w której został utworzony rejestr. Replikę domową można usunąć tylko przez usunięcie samego rejestru.

## <a name="geo-replication-pricing"></a>Cennik replikacji geograficznej

Replikacja geograficzna jest funkcją [jednostki SKU w warstwie Premium](container-registry-skus.md) usługi Azure Container Registry. Replikacja rejestru w żądanych regionach wiąże się z naliczaniem opłat za rejestr w warstwie Premium w danym regionie.

W poprzednim przykładzie firma Contoso skonsolidowała dwa rejestry w ramach jednego, dodając repliki do regionu Wschodnie stany USA, Kanada Środkowa i Europa Zachodnia. Firma Contoso zapłaciłaby miesięcznie czterokrotną opłatę za warstwę Premium bez dodatkowej konfiguracji ani zarządzania. W każdym regionie obrazy są teraz ściągane lokalnie, co zwiększa wydajność i niezawodność bez ponoszenia opłat za ruch wychodzący w sieci z regionu Zachodnie stany USA do regionu Kanada Środkowa i Wschodnie stany USA.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Rozwiązywanie problemów z operacjami wypychania za pomocą rejestrów replikowanych geograficznie
 
Klient platformy Docker, który wypycha obraz do rejestru replikowanego geograficznie, może nie wypchnąć wszystkich warstw obrazu i jego manifestu do pojedynczego regionu replikowanego. Może tak wystąpić, ponieważ usługa Azure Traffic Manager kieruje żądania rejestru do najbliższego replikowanego rejestru w sieci. Jeśli rejestr ma dwa *pobliskie* regiony replikacji, warstwy obrazu i manifest mogą być dystrybuowane do dwóch lokacji, a operacja wypychania kończy się niepowodzeniem po sprawdzeniu poprawności manifestu. Ten problem występuje ze względu na sposób, w jaki nazwa DNS rejestru jest rozpoznawana na niektórych hostach systemu Linux. Ten problem nie występuje w systemie Windows, który zapewnia pamięć podręczną DNS po stronie klienta.
 
Jeśli wystąpi ten problem, jednym z rozwiązań jest `dnsmasq` zastosowanie pamięci podręcznej DNS po stronie klienta, takiej jak na hoście systemu Linux. Pomaga to zapewnić, że nazwa rejestru jest rozpoznawana konsekwentnie. Jeśli używasz maszyny Wirtualnej z systemem Linux na platformie Azure do wypychania do rejestru, zobacz opcje w [opcjach rozpoznawania nazw DNS dla maszyn wirtualnych systemu Linux na platformie Azure](../virtual-machines/linux/azure-dns.md).

Aby zoptymalizować rozpoznawanie dns do najbliższej repliki podczas wypychania obrazów, skonfiguruj rejestr replikowany geograficznie w tych samych regionach platformy Azure jako źródło operacji wypychania lub w najbliższym regionie podczas pracy poza platformą Azure.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z trzyczęściową serią samouczków dostępną w sekcji [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md). Opisano w niej tworzenie rejestru z replikacją geograficzną, tworzenie kontenera, a następnie wdrażanie go w ramach wielu regionalnych aplikacji usługi Web Apps na potrzeby wystąpień kontenerów za pomocą pojedynczego polecenia `docker push`.

> [!div class="nextstepaction"]
> [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md)
