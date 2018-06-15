---
title: Jak zaktualizować usługa w chmurze | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaktualizować usługi w chmurze na platformie Azure. Dowiedz się, jak aktualizacja usługi w chmurze będzie kontynuowana, aby zapewnić dostępność.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 36d4ee9dabd39f4103d17455e47521b378af6ebb
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/16/2017
ms.locfileid: "24860675"
---
# <a name="how-to-update-a-cloud-service"></a>Jak zaktualizować usługa w chmurze

Aktualizowanie usługi w chmurze, tym zarówno role i system operacyjny, gościa jest procesem trzech fazach. Najpierw należy przekazać pliki binarne i pliki konfiguracji dla nowej usługi w chmurze lub wersja systemu operacyjnego. Następnie Azure rezerwuje zasobów obliczeniowych i sieci dla usługi w chmurze na podstawie wymagań nowa wersja usługi chmury. Na koniec na platformie Azure przeprowadzane uaktualnienie stopniowe, aby zaktualizować przyrostowo dzierżawcy do nowej wersji lub Gość systemu operacyjnego, zachowując jego dostępność. W tym artykule omówiono szczegóły ten ostatni krok — uaktualnienie stopniowe.

## <a name="update-an-azure-service"></a>Aktualizacja usługi Azure
Azure organizuje wystąpienia roli w logiczne grupy o nazwie domen uaktualnienia (UD). Domen uaktualnienia (UD) to logiczne zestawy wystąpień roli, które zostały zaktualizowane w grupie.  Aktualizacje platformy Azure w chmurze usługi UD jeden jednocześnie, dzięki czemu wystąpień w innych UDs, aby kontynuować, obsługująca ruch.

Domyślna liczba domen uaktualnienia wynosi 5. Można określić różne liczby domen uaktualnienia w tym atrybucie upgradeDomainCount w pliku definicji usługi (csdef). Aby uzyskać więcej informacji na temat atrybutu upgradeDomainCount, zobacz [schematu sieć Web](https://msdn.microsoft.com/library/azure/gg557553.aspx) lub [schematu proces roboczy](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Po wykonaniu aktualizacji w miejscu z jednego lub większej liczby ról w usłudze Azure aktualizuje zestawy wystąpień roli zgodnie z domeną uaktualnienia, do którego należą. Aktualizacji platformy Azure, wszystkich wystąpień dla określonej domeny uaktualnienia — zatrzymywanie, aktualizowanie, przełącza je w tryb online — powrotem następnie przechodzi do następnej domeny. Zatrzymując tylko wystąpienia uruchomione w bieżącej domenie uaktualnienia Azure upewnia się, nastąpi aktualizacja najniższe możliwe wpływu na uruchomioną usługę. Aby uzyskać więcej informacji, zobacz [jak przebieg aktualizacji](#howanupgradeproceeds) dalszej części tego artykułu.

> [!NOTE]
> Podczas warunki **aktualizacji** i **uaktualnienia** ma nieco inne znaczenie w kontekście Azure, używane zamiennie procesów oraz opisy funkcji w tym dokumencie.
>
>

Usługi należy zdefiniować co najmniej dwóch wystąpień roli dla tej roli, należy zaktualizować w miejscu bez przestoju. Jeśli usługa zawiera tylko jedno wystąpienie jedną rolę, usługi będą niedostępne do momentu zakończenia aktualizacji w miejscu.

W tym temacie omówiono następujące informacje o aktualizacji platformy Azure:

* [Dozwolone zmiany usługi podczas aktualizacji](#AllowedChanges)
* [Jak będzie kontynuowana uaktualnienia](#howanupgradeproceeds)
* [Wycofywanie aktualizacji](#RollbackofanUpdate)
* [Inicjowanie wielu operacji mutating na bieżące wdrożenia.](#multiplemutatingoperations)
* [Rozkład ról domen uaktualnienia](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Dozwolone zmiany usługi podczas aktualizacji
W poniższej tabeli przedstawiono dozwolone zmiany do usługi podczas aktualizacji:

| Zmiany dozwolone hosting usług i ról | Aktualizacja w miejscu | Przemieszczanego (wymiany wirtualnych adresów IP) | Usuń i ponownie wdróż |
| --- | --- | --- | --- |
| Wersja systemu operacyjnego |Tak |Tak |Tak |
| Poziom zaufania platformy .NET |Tak |Tak |Tak |
| Rozmiar maszyny wirtualnej<sup>1</sup> |Tak<sup>2</sup> |Tak |Tak |
| Ustawienia magazynu lokalnego |Zwiększ tylko<sup>2</sup> |Tak |Tak |
| Dodawanie lub usuwanie ról w usłudze |Tak |Tak |Tak |
| Liczba wystąpień określonej roli |Tak |Tak |Tak |
| Liczba lub typ punktów końcowych dla usługi |Tak<sup>2</sup> |Nie |Tak |
| Nazwy i wartości ustawień konfiguracji |Tak |Tak |Tak |
| Wartości (ale nie nazwy) ustawień konfiguracji |Tak |Tak |Tak |
| Dodaj nowe certyfikaty |Tak |Tak |Tak |
| Zmiana istniejących certyfikatów |Tak |Tak |Tak |
| Wdrażanie nowego kodu |Tak |Tak |Tak |

<sup>1</sup> rozmiaru zmiany ograniczony do podzbioru dostępne dla usługi w chmurze.

<sup>2</sup> wymaga zestawu SDK platformy Azure w wersji 1.5 lub nowszego.

> [!WARNING]
> Zmiana rozmiaru maszyny wirtualnej spowoduje zniszczenia danych lokalnych.
>
>

Następujące elementy nie są obsługiwane podczas aktualizacji:

* Zmiana nazwy roli. Usuń, a następnie dodaj rolę z nową nazwą.
* Zmiana liczby domen uaktualnienia.
* Zmniejszenie rozmiaru zasobów lokalnych.

W przypadku wprowadzania inne aktualizacje definicji usługi, takie jak zmniejszyć rozmiar zasobu lokalnego zamiast tego należy wykonać aktualizację wymiany adresów VIP. Aby uzyskać więcej informacji, zobacz [wymiany wdrożenia](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Jak będzie kontynuowana uaktualnienia
Można zdecydować, czy chcesz zaktualizować wszystkie role w usłudze lub jedną rolę w usłudze. W obu przypadkach wszystkie wystąpienia każdej roli, który jest uaktualniany i muszą należeć do pierwszej domeny uaktualnienia są zatrzymane, uaktualnione i przywrócony do trybu online. Po ich przywróceniu online, wystąpień w drugiej domenie uaktualnienia są zatrzymane, uaktualnione i przywrócony do trybu online. Usługi w chmurze może mieć co najwyżej jedno uaktualnienie active naraz. Uaktualnianie jest zawsze przeprowadzane przy użyciu najnowszej wersji usługi w chmurze.

Na poniższym diagramie przedstawiono, jak uaktualnianie będzie kontynuowane w przypadku uaktualniania wszystkich ról w usłudze:

![Uaktualnij usługę](media/cloud-services-update-azure-service/IC345879.png "uaktualnianie usługi")

Ten dalej diagramie przedstawiono, jak aktualizacja będzie kontynuowana, Jeśli uaktualniasz tylko jedną rolę.

![Uaktualnienie roli](media/cloud-services-update-azure-service/IC345880.png "uaktualnienia roli")  

Podczas aktualizacji automatycznych z kontrolerem sieci szkieletowej Azure okresowo ocenia kondycję usługi w chmurze do określania, kiedy jest bezpieczne przeprowadzenie UD dalej. Tej oceny kondycji jest wykonywane na podstawie-role i uwzględnia tylko wystąpienia w najnowszej wersji (tj. wystąpień z UDs, które już zostały udał). Sprawdza, czy minimalna liczba wystąpień roli dla każdej roli, zostały osiągnięte zadowalające stanu terminala.

### <a name="role-instance-start-timeout"></a>Limit czasu uruchomienia wystąpienia roli
Kontroler sieci szkieletowej będą Odczekaj 30 minut dla każdego wystąpienia roli do przejścia rozpoczęte. Jeśli upłynie limit czasu, z kontrolerem sieci szkieletowej będą nadal przejście do następnego wystąpienia roli.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Uaktualnia wpływu na dysk danych w usłudze w chmurze

Podczas uaktualniania do wielu wystąpień jednego wystąpienia usługi z usługą zostanie przeniesiony w dół podczas uaktualniania odbywa się ze względu na sposób usług Azure uaktualnienia. Dostępność usługi umowę dotyczącą poziomu gwarantujących usługi ma zastosowanie tylko do usług, które zostały wdrożone za pomocą więcej niż jedno wystąpienie. Na poniższej liście opisano wpływ danych na każdym dysku każdego scenariusza uaktualniania usług Azure:

|Scenariusz|Dysku c.|Dysk D|Dysk E|
|--------|-------|-------|-------|
|Ponowne uruchomienie maszyny Wirtualnej|Zachowane|Zachowane|Zachowane|
|Ponowne uruchomienie portalu|Zachowane|Zachowane|Zniszczone|
|Portal odtworzenia z obrazu|Zachowane|Zniszczone|Zniszczone|
|Uaktualnienie w miejscu|Zachowane|Zachowane|Zniszczone|
|Węzeł migracji|Zniszczone|Zniszczone|Zniszczone|

Należy pamiętać, że na liście powyżej dysku E: reprezentuje roli katalog główny dysku, a nie powinien być ustalony. Zamiast tego należy użyć **RoleRoot %** zmiennej środowiskowej do reprezentowania dysku.

Aby zminimalizować czas przestoju, podczas uaktualniania jednego wystąpienia usługi, należy wdrożyć nową usługę wielowystąpieniowy na serwerze i przeprowadzić wymiany wirtualnych adresów IP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Wycofywanie aktualizacji
Azure zapewnia elastyczność w zarządzaniu usług podczas aktualizacji, co pozwala na zainicjowanie dodatkowe operacje w usłudze, po zaakceptowaniu żądania początkowego aktualizacji przez kontroler sieci szkieletowej Azure. Wycofywania można wykonać tylko w przypadku aktualizacji (zmiana konfiguracji) lub Trwa uaktualnianie **w toku** stanu wdrożenia. Aktualizacji lub uaktualnieniu uważa się trwa tak długo, jak istnieje co najmniej jedno wystąpienie usługi, który nie został jeszcze zaktualizowany do nowej wersji. Aby sprawdzić, czy jest dozwolone wycofanie, sprawdź wartość flagi RollbackAllowed, zwracane przez [uzyskać wdrożenia](https://msdn.microsoft.com/library/azure/ee460804.aspx) i [pobrać właściwości usługi chmury](https://msdn.microsoft.com/library/azure/ee460806.aspx) operacje, jest ustawiony na wartość true.

> [!NOTE]
> Tylko warto wywołań wycofania na **w miejscu** aktualizacji lub uaktualnienia, ponieważ wymagają uaktualnienia wymiany adresów VIP zastępowanie jeden cały działającego wystąpienia usługi z inną.
>
>

Wycofywanie aktualizacji w toku ma następujące skutki wdrożenia:

* Wszystkie wystąpienia roli, które były jeszcze nie zostały zaktualizowane lub uaktualnione do nowej wersji nie są zaktualizowane lub uaktualnione, ponieważ już uruchomionych wystąpień tych wersji docelowej usługi.
* Wszystkie wystąpienia roli, które były już zostały zaktualizowane lub uaktualnione do nowej wersji pakietu z (\*cspkg) pliku lub konfiguracji usługi (\*.cscfg) pliku (lub oba pliki) są przywracane do wersji sprzed uaktualnienia tych plików.

Funkcjonalnie odbywa się przez następujące funkcje:

* [Wycofywania aktualizacji lub uaktualnienia](https://msdn.microsoft.com/library/azure/hh403977.aspx) operacja, która może być wywoływana dla aktualizacji konfiguracji (wyzwalane przez wywołanie metody [zmiany konfiguracji wdrożenia](https://msdn.microsoft.com/library/azure/ee460809.aspx)) lub uaktualnienia (wyzwalane przez wywołanie metody [uaktualniania wdrożenia](https://msdn.microsoft.com/library/azure/ee460793.aspx)) tak długo, jak istnieje co najmniej jedno wystąpienie usługi, który nie został jeszcze zaktualizowany do nowej wersji.
* Element zablokowane i elementu RollbackAllowed, które są zwracane jako część treści odpowiedzi [uzyskać wdrożenia](https://msdn.microsoft.com/library/azure/ee460804.aspx) i [pobrać właściwości usługi chmury](https://msdn.microsoft.com/library/azure/ee460806.aspx) operacje:

  1. Element zablokowany pozwala na wykrycie mutating operację można wywołać w ramach danego wdrożenia.
  2. RollbackAllowed element umożliwia wykrywanie, kiedy [wycofywania aktualizacji lub uaktualnienia](https://msdn.microsoft.com/library/azure/hh403977.aspx) operację można wywołać w danym wdrożeniu.

  Aby można było przeprowadzić wycofanie, jest konieczne Sprawdź zarówno zablokowane i elementy RollbackAllowed. Wystarczające, aby upewnić się, że RollbackAllowed jest ustawiony na wartość true. Te elementy są zwracane tylko, jeśli te metody są wywoływane przy użyciu nagłówek żądania ustawioną wartość "x-ms-version: 2011-10-01" lub nowszej wersji. Aby uzyskać więcej informacji o nagłówkach przechowywania wersji, zobacz [przechowywanie wersji usługi zarządzania](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Istnieje kilka sytuacji, gdy wycofywania aktualizacji lub uaktualnienie nie jest obsługiwane, są to następujące dane:

* Spadek zużycia zasobów lokalnych — Jeśli aktualizacja powoduje zwiększenie zasobów lokalnych roli platformy Azure nie zezwala na wycofanie.
* Limity przydziału — Jeśli aktualizacja została skali w dół operację, którą użytkownik może już nie ma wystarczającego przydziału obliczeń do wykonania operacji wycofywania. Każda subskrypcja platformy Azure ma limit przydziału skojarzonych z nim określająca maksymalną liczbę rdzeni, które mogą być używane przez wszystkie usługi hostowanej, które należą do tej subskrypcji. Jeśli wykonania operacji wycofywania aktualizacji danego spowodowałaby subskrypcję za pośrednictwem przydziału, a następnie który wycofanie nie zostaną włączone.
* Sytuacja wyścigu — Jeśli ukończono aktualizowanie początkowej, wycofanie nie jest możliwe.

Jest przykładem podczas wycofywania aktualizacji mogą być przydatne, jeśli używasz [uaktualniania wdrożenia](https://msdn.microsoft.com/library/azure/ee460793.aspx) jest wprowadzanie operacji w trybie ręcznym kontrolować szybkość, z jaką usługa hostowana główna uaktualnienia w miejscu do platformy Azure.

Podczas wdrażania uaktualnienia należy wywołać [uaktualniania wdrożenia](https://msdn.microsoft.com/library/azure/ee460793.aspx) w trybie ręcznym i rozpocząć przeprowadzenie uaktualnienia domen. Jeśli w pewnym momencie podczas monitorowania uaktualnienia, należy zaznaczyć kilka wystąpień roli w pierwszych domen uaktualnienia, które należy zbadać ma przestać odpowiadać, można wywołać [wycofywania aktualizacji lub uaktualnienia](https://msdn.microsoft.com/library/azure/hh403977.aspx) operację wdrożenia, co spowoduje pozostawienie niezmienione wystąpień, które nie ma jeszcze uaktualniona i wystąpienia wycofania, które miały został uaktualniony do poprzedniego pakiet usługi i konfiguracji.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Inicjowanie wielu operacji mutating na bieżące wdrożenia.
W niektórych przypadkach można zainicjować wiele równoczesnych operacji mutating na bieżące wdrożenia. Na przykład może wykonywać aktualizacji usługi i, gdy aktualizacja jest rozwijane między usługą, mają być niektóre zmiany, np. wycofanie ponownie aktualizację, zastosować inną aktualizację, lub nawet usuwać wdrożenia. Przypadek, w którym może to być konieczne jest, czy uaktualnianie usługi zawiera buggy kodu, co powoduje, że wystąpienie roli uaktualnionego wielokrotnie awarię. W tym przypadku kontrolera sieci szkieletowej Azure nie będzie postęp w zastosowaniu której uaktualnić, ponieważ określono za małą liczbę wystąpień w uaktualnionym domeny są w dobrej kondycji. Ten stan jest określany jako *zatrzymane wdrożenia*. Wdrożenia można przylegał Wycofywanie aktualizacji lub stosowania aktualizacji świeże na górze niepowodzenie jeden.

Po początkowej żądanie aktualizacji lub uaktualnienia usługi zostały odebrane przez kontroler sieci szkieletowej Azure, możesz przystąpić do kolejnych operacji mutacja. Oznacza to, że nie masz oczekiwania na zakończenie przed rozpoczęciem inna operacja mutating początkowej operacji.

Inicjowanie drugiej operacji aktualizacji, podczas pierwszej aktualizacji jest wykonywana będzie wykonywać podobne do operacji wycofywania. Druga aktualizacja jest w trybie automatycznym, pierwsza domena uaktualnienia zostanie uaktualniony natychmiast, prawdopodobnie prowadzące do wystąpienia z wielu domen uaktualnienia jest w trybie offline, w tym samym punkcie w czasie.

Operacje mutating są następujące: [zmiany konfiguracji wdrożenia](https://msdn.microsoft.com/library/azure/ee460809.aspx), [uaktualniania wdrożenia](https://msdn.microsoft.com/library/azure/ee460793.aspx), [stan wdrożenia aktualizacji](https://msdn.microsoft.com/library/azure/ee460808.aspx), [usunąć wdrożenia](https://msdn.microsoft.com/library/azure/ee460815.aspx), i [wycofywania aktualizacji lub uaktualnienia](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Dwie operacje [uzyskać wdrożenia](https://msdn.microsoft.com/library/azure/ee460804.aspx) i [pobrać właściwości usługi chmury](https://msdn.microsoft.com/library/azure/ee460806.aspx), zwraca flagę zablokowany, które można zbadać w celu ustalenia, czy mutating operację można wywołać w ramach danego wdrożenia.

Aby można było wywołać wersji tych metod, która zwraca flagę zablokowany, należy ustawić wartość nagłówka żądania "x-ms-version: 2011-10-01" lub nowszego. Aby uzyskać więcej informacji o nagłówkach przechowywania wersji, zobacz [przechowywanie wersji usługi zarządzania](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Rozkład ról domen uaktualnienia
Azure równomiernie rozdziela wystąpień roli dla pewnej liczby domen uaktualnienia, które można skonfigurować jako część pliku definicji (csdef) usługi. Maksymalna liczba domen uaktualnienia wynosi 20, a wartość domyślna to 5. Aby uzyskać więcej informacji na temat sposobu modyfikowania pliku definicji usługi, zobacz [schematu definicji usługi Azure (pliki csdef pliku)](cloud-services-model-and-package.md#csdef).

Na przykład jeśli rola użytkownika ma dziesięć wystąpień, domyślnie każdej z domen zawiera dwa wystąpienia. Jeśli rola użytkownika ma 14 wystąpień, cztery domen uaktualnienia zawiera trzy wystąpienia i piątej domeny zawiera dwa.

Domen uaktualnienia są oznaczone symbolem liczony od zera indeks: pierwsza domena uaktualnienia ma identyfikator równy 0, a druga domena uaktualnienia ma identyfikator równy 1 i tak dalej.

Na poniższym diagramie przedstawiono, jak usługi niż zawiera dwie role są dystrybuowane gdy usługa definiuje dwie domeny uaktualnienia. Usługa jest uruchomiona osiem wystąpień roli sieci web i dziewięć wystąpień roli procesu roboczego.

![Dystrybucji domen uaktualnienia](media/cloud-services-update-azure-service/IC345533.png "dystrybucji domen uaktualnienia")

> [!NOTE]
> Należy pamiętać, że Azure kontroluje sposób przydzielania wystąpień domen uaktualnienia. Nie jest możliwe określenie, które wystąpienia są przydzielone do domeny, które.
>
>

## <a name="next-steps"></a>Następne kroki
[Jak zarządzać usługi w chmurze](cloud-services-how-to-manage-portal.md)  
[Jak monitorować usługi w chmurze](cloud-services-how-to-monitor.md)  
[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)  
