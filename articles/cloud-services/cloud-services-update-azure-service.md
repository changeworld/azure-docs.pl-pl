---
title: Jak zaktualizować usługę w chmurze | Dokumenty firmy Microsoft
description: Dowiedz się, jak zaktualizować usługi w chmurze na platformie Azure. Dowiedz się, jak przebiega aktualizacja usługi w chmurze, aby zapewnić dostępność.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360348"
---
# <a name="how-to-update-a-cloud-service"></a>Jak zaktualizować usługę w chmurze

Aktualizowanie usługi w chmurze, w tym zarówno jej ról, jak i systemu operacyjnego gościa, jest procesem trzyetapowym. Najpierw należy przekazać pliki binarne i konfiguracyjne dla nowej usługi w chmurze lub wersji systemu operacyjnego. Następnie platforma Azure rezerwuje zasoby obliczeniowe i sieciowe dla usługi w chmurze na podstawie wymagań nowej wersji usługi w chmurze. Na koniec platforma Azure wykonuje uaktualnienie stopniowe, aby stopniowo aktualizować dzierżawę do nowej wersji lub systemu operacyjnego gościa, zachowując dostępność. W tym artykule omówiono szczegóły tego ostatniego kroku — uaktualnienia stopniowego.

## <a name="update-an-azure-service"></a>Aktualizowanie usługi platformy Azure
Platforma Azure organizuje wystąpienia roli w logiczne grupy zwane domenami uaktualnienia (UD). Domeny uaktualnienia (UD) to logiczne zestawy wystąpień ról, które są aktualizowane jako grupa.  Platforma Azure aktualizuje usługę w chmurze jeden UD naraz, co umożliwia wystąpienia w innych UD kontynuować obsługę ruchu.

Domyślna liczba domen uaktualnienia wynosi 5. Można określić inną liczbę domen uaktualnienia, dołączając atrybut upgradeDomainCount w pliku definicji usługi (csdef). Aby uzyskać więcej informacji na temat atrybutu upgradeDomainCount, zobacz [Schemat definicji usług w chmurze azure (plik csdef).](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file)

Podczas wykonywania aktualizacji w miejscu jednej lub więcej ról w usłudze, platforma Azure aktualizuje zestawy wystąpień ról zgodnie z domeną uaktualnienia, do której należą. Platforma Azure aktualizuje wszystkie wystąpienia w danej domenie uaktualnienia — zatrzymywanie ich, aktualizowanie ich, przywracanie ich on-line — a następnie przechodzi do następnej domeny. Zatrzymując tylko wystąpienia uruchomione w bieżącej domenie uaktualnienia, platforma Azure zapewnia, że aktualizacja występuje z jak najmniejszym możliwym wpływem na uruchomioną usługę. Aby uzyskać więcej informacji, zobacz [Jak aktualizacja przebiega](#howanupgradeproceeds) w dalszej części tego artykułu.

> [!NOTE]
> Podczas aktualizacji **terminów** i **uaktualnienia** mają nieco inne znaczenie w kontekście platformy Azure, mogą być używane zamiennie dla procesów i opisów funkcji w tym dokumencie.
>
>

Usługa musi zdefiniować co najmniej dwa wystąpienia roli dla tej roli, które mają być aktualizowane w miejscu bez przestojów. Jeśli usługa składa się tylko z jednego wystąpienia jednej roli, usługa będzie niedostępna do czasu zakończenia aktualizacji w miejscu.

W tym temacie omówiono następujące informacje dotyczące aktualizacji platformy Azure:

* [Dozwolone zmiany usługi podczas aktualizacji](#AllowedChanges)
* [Jak przebiega uaktualnienie](#howanupgradeproceeds)
* [Wycofywanie aktualizacji](#RollbackofanUpdate)
* [Inicjowanie wielu operacji mutowania podczas trwającego wdrażania](#multiplemutatingoperations)
* [Dystrybucja ról w domenach uaktualnienia](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Dozwolone zmiany usługi podczas aktualizacji
W poniższej tabeli przedstawiono dozwolone zmiany w usłudze podczas aktualizacji:

| Zmiany dozwolone w hostingu, usługach i rolach | Aktualizacja w miejscu | Etapowa (zamiana VIP) | Usuwanie i ponowne wdrażanie |
| --- | --- | --- | --- |
| Wersja systemu operacyjnego |Tak |Tak |Tak |
| Poziom zaufania platformy .NET |Tak |Tak |Tak |
| Rozmiar maszyny wirtualnej<sup>1</sup> |Tak<sup>2</sup> |Tak |Tak |
| Ustawienia magazynu lokalnego |Zwiększ tylko<sup>2</sup> |Tak |Tak |
| Dodawanie lub usuwanie ról w usłudze |Tak |Tak |Tak |
| Liczba wystąpień określonej roli |Tak |Tak |Tak |
| Liczba lub typ punktów końcowych usługi |Tak<sup>2</sup> |Nie |Tak |
| Nazwy i wartości ustawień konfiguracji |Tak |Tak |Tak |
| Wartości (ale nie nazwy) ustawień konfiguracji |Tak |Tak |Tak |
| Dodawanie nowych certyfikatów |Tak |Tak |Tak |
| Zmienianie istniejących certyfikatów |Tak |Tak |Tak |
| Wdrażanie nowego kodu |Tak |Tak |Tak |

<sup>1</sup> Zmiana rozmiaru ograniczona do podzbioru rozmiarów dostępnych dla usługi w chmurze.

<sup>2</sup> Wymaga platformy Azure SDK 1.5 lub nowszych wersji.

> [!WARNING]
> Zmiana rozmiaru maszyny wirtualnej spowoduje zniszczenie danych lokalnych.
>
>

Następujące elementy nie są obsługiwane podczas aktualizacji:

* Zmiana nazwy roli. Usuń, a następnie dodaj rolę o nowej nazwie.
* Zmiana liczby domen uaktualnienia.
* Zmniejszanie rozmiaru zasobów lokalnych.

Jeśli dokonujesz innych aktualizacji definicji usługi, takich jak zmniejszenie rozmiaru zasobu lokalnego, należy wykonać aktualizację wymiany VIP zamiast tego. Aby uzyskać więcej informacji, zobacz [Swap Deployment](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak przebiega uaktualnienie
Można zdecydować, czy chcesz zaktualizować wszystkie role w usłudze lub pojedynczą rolę w usłudze. W obu przypadkach wszystkie wystąpienia każdej roli, która jest uaktualniany i należą do pierwszej domeny uaktualnienia są zatrzymywane, uaktualniane i przywrócone do trybu online. Po powrocie do trybu online wystąpienia w drugiej domenie uaktualnienia są zatrzymywane, uaktualniane i przywracane do trybu online. Usługa w chmurze może mieć co najwyżej jedno uaktualnienie aktywne naraz. Uaktualnienie jest zawsze wykonywane w stosunku do najnowszej wersji usługi w chmurze.

Na poniższym diagramie przedstawiono, jak uaktualnianie przebiega, jeśli uaktualniasz wszystkie role w usłudze:

![Usługa uaktualnienia](media/cloud-services-update-azure-service/IC345879.png "Usługa uaktualnienia")

Ten następny diagram ilustruje przebieg aktualizacji w przypadku uaktualniania tylko jednej roli:

![Rola uaktualnienia](media/cloud-services-update-azure-service/IC345880.png "Rola uaktualnienia")  

Podczas automatycznej aktualizacji kontroler sieci szkieletowej platformy Azure okresowo ocenia kondycję usługi w chmurze, aby określić, kiedy można bezpiecznie przejść przez następny UD. Ta ocena kondycji jest wykonywana na podstawie roli i uwzględnia tylko wystąpienia w najnowszej wersji (tj. wystąpienia z UD, które zostały już przesunął). Sprawdza, czy minimalna liczba wystąpień roli, dla każdej roli, osiągnęły zadowalający stan terminalu.

### <a name="role-instance-start-timeout"></a>Limit czasu rozpoczęcia wystąpienia roli
Kontroler sieci szkieletowej będzie czekać 30 minut dla każdego wystąpienia roli, aby osiągnąć stan Started. Jeśli upłynie limit czasu, kontroler sieci szkieletowej będzie nadal przechodził do następnego wystąpienia roli.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Wpływ na przesyłanie danych podczas uaktualnień usługi w chmurze

Podczas uaktualniania usługi z jednego wystąpienia do wielu wystąpień usługa zostanie ścięty podczas uaktualniania jest wykonywana ze względu na sposób uaktualniania usług platformy Azure. Umowa dotyczącą poziomu usług gwarantująca dostępność usług dotyczy tylko usług, które są wdrażane z więcej niż jednym wystąpieniem. Na poniższej liście opisano, jak na dane na każdym dysku wpływa każdy scenariusz uaktualniania usługi platformy Azure:

|Scenariusz|Napęd C|D Napęd|Napęd E|
|--------|-------|-------|-------|
|Ponowne uruchomienie maszyny Wirtualnej|Zachowane|Zachowane|Zachowane|
|Ponowne uruchomienie portalu|Zachowane|Zachowane|Zniszczone|
|Portal reimage|Zachowane|Zniszczone|Zniszczone|
|Uaktualnienie w miejscu|Zachowane|Zachowane|Zniszczone|
|Migracja węzłów|Zniszczone|Zniszczone|Zniszczone|

Należy zauważyć, że na powyższej liście dysk E: reprezentuje główny dysk roli i nie powinien być zakodowany na dysku twardym. Zamiast tego należy użyć zmiennej środowiskowej **%RoleRoot%** do reprezentowania dysku.

Aby zminimalizować przestoje podczas uaktualniania usługi pojedynczego wystąpienia, należy wdrożyć nową usługę wielu wystąpień na serwerze przejściowym i przeprowadzić zamianę adresu VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Wycofywanie aktualizacji
Platforma Azure zapewnia elastyczność w zarządzaniu usługami podczas aktualizacji, umożliwiając zainicjowanie dodatkowych operacji w usłudze, po zaakceptowaniu początkowego żądania aktualizacji przez kontroler sieci szkieletowej platformy Azure. Wycofywanie można wykonać tylko wtedy, gdy aktualizacja (zmiana konfiguracji) lub uaktualnienie jest w stanie **w toku** wdrożenia. Aktualizacja lub uaktualnienie jest uważane za w toku, o ile istnieje co najmniej jedno wystąpienie usługi, która nie została jeszcze zaktualizowana do nowej wersji. Aby sprawdzić, czy wycofywanie jest dozwolone, sprawdź wartość flagy RollbackAllowed, zwróconej przez operacje [Pobierz wdrożenie](/previous-versions/azure/reference/ee460804(v=azure.100)) i Pobierz właściwości usługi [w chmurze,](/previous-versions/azure/reference/ee460806(v=azure.100)) jest ustawiona na true.

> [!NOTE]
> Warto tylko wywołać wycofywanie w aktualizacji lub uaktualnieniu **w miejscu,** ponieważ uaktualnienia wymiany VIP obejmują zastąpienie jednego całego uruchomionego wystąpienia usługi innym.
>
>

Wycofywanie aktualizacji w toku ma następujący wpływ na wdrożenie:

* Wszystkie wystąpienia roli, które nie zostały jeszcze zaktualizowane lub uaktualnione do nowej wersji, nie są aktualizowane ani uaktualniane, ponieważ te wystąpienia są już uruchomione docelową wersję usługi.
* Wszystkie wystąpienia roli, które zostały już zaktualizowane lub uaktualnione\*do nowej wersji pliku pakietu usług\*( .cspkg) lub pliku konfiguracji usługi (.cscfg) (lub obu plików), są przywracane do wersji wstępnej aktualizacji tych plików.

To funkcjonalnie jest dostarczane przez następujące funkcje:

* Operacja [wycofywania aktualizacji lub uaktualnienia,](/previous-versions/azure/reference/hh403977(v=azure.100)) która może być wywołana na aktualizację konfiguracji (wyzwalane przez [wywołanie zmień konfigurację wdrożenia)](/previous-versions/azure/reference/ee460809(v=azure.100))lub uaktualnienie (wyzwalane przez [wywołanie wdrożenia uaktualnienia),](/previous-versions/azure/reference/ee460793(v=azure.100))o ile istnieje co najmniej jedno wystąpienie w usłudze, która nie została jeszcze zaktualizowana do nowej wersji.
* Zablokowany element i RollbackAllowed element, które są zwracane jako część treści odpowiedzi [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) and Get Cloud [Service Properties](/previous-versions/azure/reference/ee460806(v=azure.100)) operacji:

  1. Zablokowany element pozwala wykryć, kiedy mutowanie operacji można wywołać w danym wdrożeniu.
  2. RollbackAllowed element pozwala wykryć, kiedy [wycofywania aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) operacji można wywołać w danym wdrożeniu.

  Aby wykonać wycofywanie, nie trzeba sprawdzać zarówno zablokowane i RollbackAllowed elementów. Wystarczy potwierdzić, że rollbackallowed jest ustawiona na true. Te elementy są zwracane tylko wtedy, gdy te metody są wywoływane przy użyciu nagłówka żądania ustawionego na "x-ms-version: 2011-10-01" lub nowszej wersji. Aby uzyskać więcej informacji na temat przechowywania nagłówków, zobacz [Przechowywanie wersji zarządzania usługami](/previous-versions/azure/gg592580(v=azure.100)).

Istnieją sytuacje, w których wycofywanie aktualizacji lub uaktualnienia nie jest obsługiwane, są następujące:

* Zmniejszenie zasobów lokalnych — jeśli aktualizacja zwiększa zasoby lokalne dla roli platformy Azure nie zezwala na wycofywanie.
* Ograniczenia przydziału — jeśli aktualizacja była operacją skalowania w dół, może nie być już wystarczający przydział obliczeniowy, aby zakończyć operację wycofywania. Każda subskrypcja platformy Azure ma przydział skojarzony z nim, który określa maksymalną liczbę rdzeni, które mogą być używane przez wszystkie usługi hostowane, które należą do tej subskrypcji. Jeśli wykonanie wycofywania danej aktualizacji spowoduje umieszczenie subskrypcji za pomocą przydziału, wówczas wycofywanie nie zostanie włączone.
* Stan wyścigu — jeśli początkowa aktualizacja została zakończona, wycofywanie nie jest możliwe.

Przykładem, kiedy wycofywanie aktualizacji może być przydatne, jest, jeśli używasz operacji [wdrażania uaktualnienia](/previous-versions/azure/reference/ee460793(v=azure.100)) w trybie ręcznym, aby kontrolować szybkość, z jaką jest wdrażane główne uaktualnienie w miejscu do usługi hosta platformy Azure.

Podczas wdrażania uaktualnienia można wywołać [wdrożenie uaktualnienia](/previous-versions/azure/reference/ee460793(v=azure.100)) w trybie ręcznym i rozpocząć chodzenie domen uaktualnienia. Jeśli w pewnym momencie podczas monitorowania uaktualnienia należy zauważyć, że niektóre wystąpienia roli w pierwszych domenach uaktualnienia, które zostały zbadane, przestały odpowiadać, można wywołać operację [aktualizacji wycofywania lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)) podczas wdrażania, co pozostawi nietknięte wystąpienia, które nie zostały jeszcze uaktualnione i wycofane wystąpienia, które zostały uaktualnione do poprzedniego pakietu usług i konfiguracji.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Inicjowanie wielu operacji mutowania podczas trwającego wdrażania
W niektórych przypadkach można zainicjować wiele jednoczesnych operacji mutowania podczas trwającego wdrażania. Na przykład można wykonać aktualizację usługi i, gdy ta aktualizacja jest wdrażana w całej usłudze, chcesz wprowadzić pewne zmiany, na przykład, aby wycofać aktualizację, zastosować inną aktualizację, a nawet usunąć wdrożenie. Przypadek, w którym może to być konieczne, jeśli uaktualnienie usługi zawiera kod błędów, który powoduje, że uaktualnione wystąpienie roli wielokrotnie ulega awarii. W takim przypadku kontroler sieci szkieletowej platformy Azure nie będzie mógł poczynić postępów w stosowaniu tego uaktualnienia, ponieważ niewystarczająca liczba wystąpień w uaktualnionej domenie jest w dobrej kondycji. Ten stan jest określany jako *zablokowane wdrożenie*. Można odkleić wdrożenia, cofając aktualizację lub stosując nową aktualizację w stosunku do nieudanej.

Po odebraniu początkowego żądania aktualizacji lub uaktualnienia usługi przez kontroler sieci szkieletowej platformy Azure można rozpocząć kolejne operacje mutowania. Oznacza to, że nie trzeba czekać na zakończenie początkowej operacji przed rozpoczęciem innej operacji mutowania.

Inicjowanie drugiej operacji aktualizacji, gdy pierwsza aktualizacja jest w toku, będzie działać podobnie do operacji wycofywania. Jeśli druga aktualizacja jest w trybie automatycznym, pierwsza domena uaktualnienia zostanie uaktualniona natychmiast, co prawdopodobnie prowadzi do wystąpienia z wielu domen uaktualniania w trybie offline w tym samym momencie w czasie.

Operacje mutowania są następujące: [Zmień konfigurację wdrożenia,](/previous-versions/azure/reference/ee460809(v=azure.100)) [Wdrożenie uaktualnienia,](/previous-versions/azure/reference/ee460793(v=azure.100)) [Stan aktualizacji wdrożenia,](/previous-versions/azure/reference/ee460808(v=azure.100)) [Usuń wdrożenie](/previous-versions/azure/reference/ee460815(v=azure.100))i [Wycofywanie aktualizacji lub uaktualnienia](/previous-versions/azure/reference/hh403977(v=azure.100)).

Dwie operacje, [Pobierz wdrożenia](/previous-versions/azure/reference/ee460804(v=azure.100)) i [Pobierz właściwości usługi w chmurze](/previous-versions/azure/reference/ee460806(v=azure.100)), zwraca flagę Zablokowane, które mogą być badane w celu ustalenia, czy operacja mutowania może być wywoływana w danym wdrożeniu.

Aby wywołać wersję tych metod, która zwraca flagę Zablokowane, należy ustawić nagłówek żądania na "x-ms-version: 2011-10-01" lub nowsze. Aby uzyskać więcej informacji na temat przechowywania nagłówków, zobacz [Przechowywanie wersji zarządzania usługami](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Dystrybucja ról w domenach uaktualnienia
Platforma Azure równomiernie dystrybuuje wystąpienia roli w określonej liczbie domen uaktualnienia, które można skonfigurować jako część pliku definicji usługi (csdef). Maksymalna liczba domen uaktualnienia wynosi 20, a wartość domyślna to 5. Aby uzyskać więcej informacji na temat modyfikowania pliku definicji usługi, zobacz [Schemat definicji usługi Azure (plik csdef).](cloud-services-model-and-package.md#csdef)

Na przykład jeśli rola ma dziesięć wystąpień, domyślnie każda domena uaktualnienia zawiera dwa wystąpienia. Jeśli twoja rola ma 14 wystąpień, cztery domeny uaktualnienia zawierają trzy wystąpienia, a piąta domena zawiera dwa.

Domeny uaktualnienia są identyfikowane za pomocą indeksu opartego na wartości zerowej: pierwsza domena uaktualnienia ma identyfikator 0, a druga domena uaktualnienia ma identyfikator 1 i tak dalej.

Na poniższym diagramie przedstawiono, jak usługa niż zawiera dwie role są dystrybuowane, gdy usługa definiuje dwie domeny uaktualnienia. Usługa jest uruchomiona osiem wystąpień roli sieci web i dziewięć wystąpień roli procesu roboczego.

![Dystrybucja domen uaktualnienia](media/cloud-services-update-azure-service/IC345533.png "Dystrybucja domen uaktualnienia")

> [!NOTE]
> Należy zauważyć, że platforma Azure kontroluje sposób przydzielania wystąpień między domenami uaktualnienia. Nie można określić, które wystąpienia są przydzielane do której domeny.
>
>

## <a name="next-steps"></a>Następne kroki
[Jak zarządzać usługami w chmurze](cloud-services-how-to-manage-portal.md)  
[Jak monitorować usługi w chmurze](cloud-services-how-to-monitor.md)  
[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)  



