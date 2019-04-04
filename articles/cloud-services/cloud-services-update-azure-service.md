---
title: Jak zaktualizować usługę w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaktualizować usług w chmurze na platformie Azure. Dowiedz się, jak aktualizacji w usłudze w chmurze przechodzi do zapewnienia dostępności.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ff4dd571911719e4f2ec27952785432960a56d42
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917230"
---
# <a name="how-to-update-a-cloud-service"></a>Jak zaktualizować usługę w chmurze

Aktualizowanie usługi w chmurze, łącznie z jej ról i gościa systemu operacyjnego, jest procesem trzech etapów. Najpierw należy przekazać pliki binarne i pliki konfiguracji dla nowej usługi w chmurze lub wersję systemu operacyjnego. Następnie platforma Azure rezerwuje zasobów obliczeniowych i sieci dla usługi w chmurze na podstawie wymagań nowej wersji usługi w chmurze. Ponadto platforma Azure przeprowadza uaktualnienie stopniowe przyrostowo aktualizacji do nowej wersji systemu operacyjnego, gościa dzierżawy przy jednoczesnym zachowaniu dostępności usługi. W tym artykule omówiono szczegóły ten ostatni krok — uaktualnienia stopniowego.

## <a name="update-an-azure-service"></a>Aktualizacja usługi platformy Azure
Azure organizuje wystąpień roli w logiczne grupy o nazwie domen uaktualnienia (UD). Domen uaktualnienia (UD) to logiczne zestawy wystąpień roli, które zostały zaktualizowane w grupie.  Aktualizacje platformy Azure w chmurze usługi UD jednego naraz, dzięki czemu wystąpień w innych domenach uaktualniania można nadal obsługiwać ruch.

Domyślna liczba domen uaktualnienia wynosi 5. Można określić różną liczbę domen uaktualnienia, umieszczając atrybut upgradeDomainCount w pliku definicji usługi (csdef). Aby uzyskać więcej informacji na temat atrybutów upgradeDomainCount zobacz [webrole — schemat](/previous-versions/azure/reference/gg557553(v=azure.100)) lub [workerrole — schemat](/previous-versions/azure/reference/gg557552(v=azure.100)).

Po wykonaniu aktualizacji w miejscu z jednego lub większej liczby ról w usłudze Azure aktualizuje zestawy wystąpień roli, zgodnie z domeną uaktualnienia, do której należą. Aktualizacje platformy Azure, wszystkich wystąpień w danej domenie uaktualnienia — zatrzymywanie, aktualizuje je, wskazanie ich z powrotem w trybie online — następnie przechodzi do następnej domeny. Przez zatrzymanie wystąpienia, uruchomione w bieżącej domenie uaktualnienia, Azure zapewnia, że nastąpi aktualizacja przy najniższe możliwe wpływie na uruchomioną usługę. Aby uzyskać więcej informacji, zobacz [jak aktualizacja będzie kontynuowana](#howanupgradeproceeds) w dalszej części tego artykułu.

> [!NOTE]
> Podczas gdy warunki **aktualizacji** i **uaktualnienia** ma nieco inne znaczenie w kontekście platformy Azure, używane zamiennie dla procesów i opisy funkcji w tym dokumencie.
>
>

Usługi, należy zdefiniować co najmniej dwóch wystąpień roli dla tej roli, należy zaktualizować w miejscu bez przestojów. Jeśli usługa składa się tylko jedno wystąpienie jedną rolę, usługi będzie niedostępna do momentu zakończenia aktualizację w miejscu.

W tym temacie omówiono następujące informacje na temat aktualizacji platformy Azure:

* [Dozwolone zmiany usługi podczas aktualizacji](#AllowedChanges)
* [Jak przechodzi uaktualnienie](#howanupgradeproceeds)
* [Wycofywanie aktualizacji](#RollbackofanUpdate)
* [Inicjowanie wielu operacji mutujące na ciągłego wdrażania](#multiplemutatingoperations)
* [Dystrybucja ról w domenach uaktualnienia](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Dozwolone zmiany usługi podczas aktualizacji
W poniższej tabeli przedstawiono dozwolone zmian z usługą podczas aktualizacji:

| Zmiany są dozwolone do hostingu, usług i ról | Aktualizacja "w miejscu" | Przygotowane (wymiany wirtualnych adresów IP) | Usunięcie i ponowne wdrażanie |
| --- | --- | --- | --- |
| Wersja systemu operacyjnego |Yes |Yes |Yes |
| Poziom zaufania platformy .NET |Yes |Yes |Yes |
| Rozmiar maszyny wirtualnej<sup>1</sup> |Tak<sup>2</sup> |Yes |Yes |
| Ustawienia magazynu lokalnego |Zwiększ tylko<sup>2</sup> |Yes |Yes |
| Dodawanie lub usuwanie ról w usłudze |Yes |Yes |Yes |
| Liczba wystąpień określonej roli |Yes |Yes |Yes |
| Liczba lub typ punktów końcowych usługi |Tak<sup>2</sup> |Nie |Yes |
| Nazwy i wartości ustawień konfiguracji |Yes |Yes |Yes |
| Wartości (ale nie nadaje) ustawień konfiguracji |Yes |Yes |Yes |
| Dodaj nowe certyfikaty |Yes |Yes |Yes |
| Zmienianie istniejących certyfikatów |Yes |Yes |Yes |
| Wdrażanie nowego kodu |Yes |Yes |Yes |

<sup>1</sup> ograniczony do podzbioru dostępnych dla usługi w chmurze rozmiarów zmiany rozmiaru.

<sup>2</sup> wymaga zestawu SDK platformy Azure w wersji 1.5 lub nowszej wersji.

> [!WARNING]
> Zmiana rozmiaru maszyny wirtualnej spowoduje zniszczenie danych lokalnych.
>
>

Następujące elementy nie są obsługiwane podczas aktualizacji:

* Zmiana nazwy roli. Usuń, a następnie dodaj rolę z nową nazwą.
* Zmiana liczby domen uaktualnienia.
* Zmniejszenie rozmiaru zasobów lokalnych.

Jeśli wykonujesz inne aktualizacje definicji usługi, takie jak zmniejszyć rozmiar zasobu lokalnego zamiast tego należy wykonać aktualizację wymiany adresu VIP. Aby uzyskać więcej informacji, zobacz [Swap Deployment](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak przechodzi uaktualnienie
Aby zdecydować, czy chcesz zaktualizować wszystkie role w usłudze lub pojedynczej roli w ramach usługi. W obu przypadkach wszystkich wystąpień każdej roli, która jest uaktualniana i należą do pierwszej domeny uaktualnienia są zatrzymane, uaktualnienia i powrócić do trybu online. Gdy są one wróci do trybu online, wystąpienia w drugiej domenie uaktualnienia są zatrzymane, uaktualnienia i powrócić do trybu online. Usługa w chmurze może mieć co najwyżej jedno uaktualnienie aktywne w danym momencie. Uaktualnienie jest zawsze przeprowadzane w najnowszej wersji usługi w chmurze.

Na poniższym diagramie przedstawiono, jak uaktualnienie będzie kontynuowane w przypadku uaktualniania wszystkich ról w usłudze:

![Uaktualnij usługę](media/cloud-services-update-azure-service/IC345879.png "Uaktualnij usługę")

Ten diagram dalej ilustruje, jak aktualizacja będzie kontynuowana, jeśli przeprowadzasz uaktualnienie pojedynczej roli:

![Uaktualnienie roli](media/cloud-services-update-azure-service/IC345880.png "uaktualnienie roli")  

Podczas aktualizacji automatycznych w kontroler sieci szkieletowej platformy Azure są okresowo ocenia kondycję usługi w chmurze do określania, kiedy jest bezpieczne zapoznać się z następnym UD. Ocena kondycji jest wykonywane na podstawie poszczególnych ról i uwzględnia tylko wystąpienia w najnowszej wersji (tj. wystąpienia z domenami aktualizacji, które mają już dodawanym). Sprawdza, czy minimalna liczba wystąpień roli, dla każdej z ról zostały osiągnięte zadowalające stan końcowy.

### <a name="role-instance-start-timeout"></a>Limit czasu uruchamiania wystąpienie roli
Kontroler sieci szkieletowej będzie czekać każdego wystąpienia roli, aby osiągnąć stan uruchomiona na 30 minut. Jeżeli upłynie limit czasu, Kontroler sieci szkieletowej będą nadal zalet do następnego wystąpienia roli.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Uaktualnia wpływu na dysku danych w usłudze w chmurze

Podczas uaktualniania usługi z pojedynczego wystąpienia do wielu wystąpień usługi będzie ulec awarii podczas uaktualniania odbywa się ze względu na sposób usług Azure uaktualnienia. Umowa dotycząca poziomu na gwarantujące dostępność usługi usługa ma zastosowanie tylko do usług, które są wdrażane przy użyciu więcej niż jedno wystąpienie. Na poniższej liście opisano wpływ scenariusza uaktualniania każda usługa danych na każdym dysku:

|Scenariusz|Dysku c.|Dysku D|Dysku E|
|--------|-------|-------|-------|
|Ponowne uruchomienie maszyny Wirtualnej|Zachowane|Zachowane|Zachowane|
|Ponowne uruchomienie komputera w portalu|Zachowane|Zachowane|Zniszczone|
|Portal odtworzenia z obrazu|Zachowane|Zniszczone|Zniszczone|
|Uaktualnienie w miejscu|Zachowane|Zachowane|Zniszczone|
|Węzeł migracji|Zniszczone|Zniszczone|Zniszczone|

Należy pamiętać, że na powyższej liście dysku E: reprezentuje rolę w katalogu głównym dysku, a nie powinny być ustalone. Zamiast tego należy użyć **RoleRoot %** zmiennej środowiskowej, aby reprezentować dysku.

Aby zminimalizować przestoje podczas uaktualniania pojedynczego wystąpienia usługi, wdrażanie nowej usługi w wielu wystąpieniach na serwerze i wykonać wymiany wirtualnych adresów IP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Wycofywanie aktualizacji
Platforma Azure zapewnia elastyczność w zakresie zarządzania usługi podczas aktualizacji, umożliwiając inicjowanie dodatkowych operacji w usłudze po zaakceptowaniu żądania aktualizacji początkowej przez kontroler sieci szkieletowej platformy Azure. Wycofywania można wykonać tylko w przypadku aktualizacji (zmiana konfiguracji) lub Trwa uaktualnianie **w toku** stanu wdrożenia. Aktualizacja lub uaktualnienia jest uważany za w toku, tak długo, jak istnieje co najmniej jedno wystąpienie usługi, który nie został jeszcze zaktualizowany do nowej wersji. Aby sprawdzić, czy jest dozwolone wycofywania, sprawdź wartość flagi RollbackAllowed, zwracany przez [Rozpoczynanie wdrażania](/previous-versions/azure/reference/ee460804(v=azure.100)) i [pobrać właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100)) operacji jest ustawiona na wartość true.

> [!NOTE]
> Tylko warto wywołanie wycofywania na **w miejscu** aktualizacji lub uaktualnienia, ponieważ obejmują uaktualnień wymiany adresu VIP, zastępując cały jedno uruchomione wystąpienie usługi z inną.
>
>

Wycofanie aktualizacja w toku ma następujące skutki wdrożenia:

* Wszystkie wystąpienia roli, które miały jeszcze nie zostały zaktualizowane lub uaktualnione do nowej wersji nie są zaktualizowane lub uaktualnione, ponieważ te wystąpienia działają już wersji docelowej usługi.
* Wszystkie wystąpienia roli, które miały już zaktualizowane lub uaktualnione do nowej wersji pakietu z (\*cspkg) pliku lub konfiguracji usługi (\*cscfg) pliku (lub oba pliki) są przywracane przed uaktualnieniem wersje tych plików.

To funkcjonalnie są dostarczane przez następujące funkcje:

* [Wycofywanie aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) operacji, która może być wywoływana po aktualizacji konfiguracji (wyzwalane przez wywołanie metody [zmiana konfiguracji wdrożenia](/previous-versions/azure/reference/ee460809(v=azure.100))) lub uaktualnienia (wyzwalane przez wywołanie metody [ Uaktualnianie wdrożenia](/previous-versions/azure/reference/ee460793(v=azure.100))) tak długo, jak istnieje co najmniej jedno wystąpienie usługi, który nie został jeszcze zaktualizowany do nowej wersji.
* Element zablokowany i RollbackAllowed element, które są zwracane jako część treści odpowiedzi [Rozpoczynanie wdrażania](/previous-versions/azure/reference/ee460804(v=azure.100)) i [pobrać właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100)) operacje:

  1. Element zablokowany umożliwia wykrywanie mutujące operacji może być wywoływany w danym wdrożeniu.
  2. RollbackAllowed element pozwala wykryć, kiedy [Wycofywanie aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) operacji może być wywoływana dla danego wdrożenia.

  Aby można było wykonać wycofanie, jest konieczne Sprawdź elementy RollbackAllowed i zablokowany. Wystarczające, aby upewnić się, że RollbackAllowed jest ustawiona na wartość true. Te elementy są zwracane tylko, jeśli te metody są wywoływane przy użyciu nagłówka żądania ustawiona na "x-ms-version: 2011-10-01 "lub nowszej. Aby uzyskać więcej informacji o nagłówkach przechowywania wersji, zobacz [przechowywanie wersji usługi zarządzania](/previous-versions/azure/gg592580(v=azure.100)).

Istnieje kilka sytuacji, gdy Wycofywanie aktualizacji lub uaktualnienie nie jest obsługiwane, są w następujący sposób:

* Zmniejszenie zasobów lokalnych — Jeśli aktualizacja zwiększa zasobów lokalnych roli platformy Azure nie zezwala na wycofywanie.
* Ograniczenia limitu przydziału — Jeśli operacja skalowania w dół możesz już zainstalowano aktualizację ma wystarczający przydział usługi compute do ukończenia operacji wycofywania. Każda subskrypcja platformy Azure ma limit przydziału skojarzonych z nim określająca maksymalną liczbę rdzeni, które mogą być używane przez wszystkich hostowanych usług, które należą do tej subskrypcji. Jeśli przeprowadzania wycofywania danej aktualizacji umieścić swoją subskrypcję za pośrednictwem przydziału, a następnie wycofanie nie zostaną włączone.
* Sytuacja wyścigu — Jeśli ukończono aktualizowanie początkowego, wycofanie nie jest możliwe.

Jest przykładem podczas wycofywania aktualizacji mogą być przydatne, jeśli używasz [uaktualniania wdrożenia](/previous-versions/azure/reference/ee460793(v=azure.100)) operacji w trybie ręcznej, aby kontrolować szybkość, z jaką usługa hostowana główna uaktualnienia w miejscu do subskrypcji platformy Azure jest wdrażana.

W miarę wprowadzania aktualizacji uaktualnienia należy wywołać [uaktualniania wdrożenia](/previous-versions/azure/reference/ee460793(v=azure.100)) w trybie ręcznej i zacząć Skontroluj domeny uaktualnienia. Jeśli w pewnym momencie podczas monitorowania uaktualnienia, należy zaznaczyć kilka wystąpień roli w pierwszym domen uaktualnienia, które badania mają przestanie odpowiadać, można wywołać [Wycofywanie aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) operację wdrożenia, co spowoduje, że niezmienione wystąpień, które nie miały jeszcze uaktualnione i wystąpień wycofywania, co ma został uaktualniony do poprzedniego pakietu usługi i konfiguracji.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Inicjowanie wielu operacji mutujące na ciągłego wdrażania
W niektórych przypadkach można zainicjować wiele jednoczesnych operacji mutujące na ciągłego wdrażania. Na przykład, możesz wykonać aktualizacji usługi i podczas tej aktualizacji jest wdrażane w usłudze, chcesz wprowadzić pewne zmiany, np. przeprowadzić aktualizację ponownie, należy zastosować inną aktualizację, lub nawet usunąć wdrożenie. Przypadek, w której może to być konieczne jest, czy uaktualnienie usługi zawiera pracowały kodu, co powoduje, że wystąpienie roli uaktualnionego wielokrotnie awarię. W tym przypadku Kontroler sieci szkieletowej platformy Azure nie będzie postęp stosowania, uaktualnić, ponieważ wystarczającej liczby wystąpień w uaktualnionym domeny są w dobrej kondycji. Ten stan jest nazywany *została zablokowana wdrożenia*. Wdrożenie może odklej Wycofywanie aktualizacji lub zastosowanie nowej aktualizacji w górnej części niepowodzenie jednego.

Po otrzymaniu początkowe żądanie aktualizacji lub uaktualnienia usługi przez kontroler sieci szkieletowej platformy Azure, można uruchomić kolejne operacje mutujące. Oznacza to, że nie trzeba czekać na zakończenie przed rozpoczęciem inna operacja mutujące początkowej operacji.

Inicjowanie drugą operację aktualizacji, gdy trwa Pierwsza aktualizacja będzie wykonywać podobne do operacji wycofywania. W przypadku drugiej aktualizacji w trybie automatycznym, pierwsza domena uaktualnienia zostaną uaktualnione natychmiast, prawdopodobnie prowadzących do wystąpienia z wielu domen uaktualnienia, jest w trybie offline, w tym samym punkcie w czasie.

Operacje mutujące są następujące: [Zmiana konfiguracji wdrożenia](/previous-versions/azure/reference/ee460809(v=azure.100)), [uaktualnić wdrożenie](/previous-versions/azure/reference/ee460793(v=azure.100)), [stan wdrożenia aktualizacji](/previous-versions/azure/reference/ee460808(v=azure.100)), [Drożenie](/previous-versions/azure/reference/ee460815(v=azure.100)), i [wycofywania Aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)).

Dwie operacje [Rozpoczynanie wdrażania](/previous-versions/azure/reference/ee460804(v=azure.100)) i [pobrać właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100)), zwracają Flaga zablokowany, która może być sprawdzane w celu określenia, czy mutujące operacji może być wywoływany w danym wdrożeniu.

Aby można było wywołać wersji tych metod, która zwraca flagę zablokowany, musisz ustawić nagłówek żądania "x-ms-version: 2011-10-01 "lub nowszej. Aby uzyskać więcej informacji o nagłówkach przechowywania wersji, zobacz [przechowywanie wersji usługi zarządzania](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Dystrybucja ról w domenach uaktualnienia
Platforma Azure rozdzieli wystąpień roli równomiernie między określona liczba domen uaktualnienia, które można skonfigurować jako część pliku definicji (csdef) usługi. Maksymalna liczba domen uaktualnienia to 20, a wartość domyślna to 5. Aby uzyskać więcej informacji na temat sposobu modyfikowania pliku definicji usługi, zobacz [schematem definicji usługi platformy Azure (csdef pliku)](cloud-services-model-and-package.md#csdef).

Na przykład jeśli Twoja rola ma dziesięciu wystąpień, domyślnie każda domena uaktualnienia zawiera dwa wystąpienia. Jeśli Twoja rola ma 14 wystąpień, czterech domenach uaktualnienia zawiera trzy wystąpienia, a piąta domeny zawiera dwa.

Domeny uaktualnień są oznaczone symbolem liczony od zera indeks: pierwsza domena uaktualnienia ma identyfikator równy 0, a drugą domenę uaktualnienia ma identyfikator równy 1 i tak dalej.

Na poniższym diagramie przedstawiono, sposób dystrybucji usługi niż zawiera dwie role, gdy usługa definiuje dwie domeny uaktualnienia. Usługa jest uruchomiona, osiem wystąpienia roli sieć web i dziewięć wystąpień roli procesu roboczego.

![Rozkład domen uaktualnienia](media/cloud-services-update-azure-service/IC345533.png "dystrybucji domen uaktualnienia")

> [!NOTE]
> Należy pamiętać, że Azure kontroluje sposób wystąpienia są przydzielane w domenach uaktualnienia. Nie jest możliwe do określenia, których wystąpienia są przydzielane do domeny, które.
>
>

## <a name="next-steps"></a>Kolejne kroki
[Jak zarządzać usługami Cloud Services](cloud-services-how-to-manage-portal.md)  
[Jak monitorować usługi Cloud Services](cloud-services-how-to-monitor.md)  
[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)  
