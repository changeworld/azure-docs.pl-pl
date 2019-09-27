---
title: Tworzenie pamięci podręcznej platformy Azure HPC (wersja zapoznawcza)
description: Jak utworzyć wystąpienie pamięci podręcznej platformy Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: v-erkell
ms.openlocfilehash: a0590c14032595bea685c69962ef27dca14d1d69
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300015"
---
# <a name="create-an-azure-hpc-cache-preview"></a>Tworzenie pamięci podręcznej platformy Azure HPC (wersja zapoznawcza)

Użyj Azure Portal, aby utworzyć pamięć podręczną. 

![zrzut ekranu przedstawiający przegląd pamięci podręcznej w Azure Portal z przyciskiem Utwórz u dołu](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiuj podstawowe szczegóły

![zrzut ekranu strony szczegółów projektu w Azure Portal](media/hpc-cache-create-basics.png)

W obszarze **szczegóły projektu**wybierz subskrypcję i grupę zasobów, w której będzie hostowana pamięć podręczna. Upewnij się, że subskrypcja znajduje się na liście [dostęp do wersji zapoznawczej](hpc-cache-prereqs.md#azure-subscription) .

W obszarze **Szczegóły usługi**Ustaw nazwę pamięci podręcznej i inne atrybuty:

* Lokalizacja — wybierz jeden z [obsługiwanych regionów](hpc-cache-overview.md#region-availability).
* Sieć wirtualna — możesz wybrać istniejącą lub utworzyć nową sieć wirtualną.
* Podsieć — wybierz lub Utwórz podsieć z co najmniej 64 adresami IP (/24), które będą używane tylko dla tego wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="set-cache-capacity"></a>Ustawianie pojemności pamięci podręcznej
<!-- referenced from GUI - update aka.ms link if you change this header text -->

Na stronie **pamięć podręczna** należy ustawić pojemność pamięci podręcznej. Ta wartość określa, ile danych może być przechowywane w pamięci podręcznej oraz jak szybko może obsłużyć żądania klientów. 

Po okresie publicznej wersji zapoznawczej pojemność wpłynie również na koszt pamięci podręcznej.

Pojemność pamięci podręcznej jest mierzona w operacjach wejścia/wyjścia na sekundę (IOPS). Wybierz pojemność, ustawiając te dwie wartości:

* Maksymalna szybkość transferu danych w pamięci podręcznej (przepływność), w GB/s
* Ilość miejsca do magazynowania przydzieloną dla danych w pamięci podręcznej w TB

Wybierz jedną z dostępnych wartości przepływności i rozmiar pamięci podręcznej. Wydajność operacji we/wy jest obliczana i pokazywana poniżej selektorów wartości.

Należy pamiętać, że Rzeczywista szybkość transferu danych zależy od obciążenia, szybkości sieci i typu miejsca docelowego magazynu. Wybrana wartość ustawia maksymalną przepływność dla całej pamięci podręcznej, a nie wszystkie są dostępne dla żądań klientów. Na przykład jeśli klient żąda pliku, który nie jest już przechowywany w pamięci podręcznej, lub jeśli plik jest oznaczony jako przestarzały, pamięć podręczna będzie używać niektórych przepływności do pobrania z magazynu zaplecza.

Pamięć podręczna platformy Azure HPC zarządza, które pliki są buforowane i wstępnie załadowane, aby zmaksymalizować szybkość trafień pamięci podręcznej. Zawartość pamięci podręcznej jest stale oceniana i pliki są przenoszone do magazynu długoterminowego, gdy są one rzadziej używane. Wybierz rozmiar pamięci podręcznej, który może wygodnie przechowywać aktywny zestaw plików roboczych z dodatkowym miejscem dla metadanych i innych obciążeń.

![zrzut ekranu strony zmiany wielkości pamięci podręcznej](media/hpc-cache-create-iops.png)

## <a name="add-storage-targets"></a>Dodawanie lokalizacji docelowych magazynu

Cele magazynu to zaplecze i długoterminowe przechowywanie zawartości pamięci podręcznej.

Można zdefiniować miejsca docelowe magazynu podczas tworzenia pamięci podręcznej, ale można je również dodać później za pomocą linku w sekcji **Konfigurowanie** strony pamięci podręcznej w portalu.

![zrzut ekranu strony miejsca docelowe magazynu](media/hpc-cache-storage-targets-pop.png)

Kliknij **link Dodaj miejsce docelowe magazynu** , aby zdefiniować systemy przechowywania zaplecza. Magazynem mogą być kontenery obiektów blob platformy Azure lub lokalne systemy plików NFS.

Można zdefiniować maksymalnie dziesięć różnych miejsc docelowych magazynu.

Instrukcje krok po kroku dotyczące dodawania miejsca docelowego magazynu znajdują się w temacie [Dodawanie elementów docelowych magazynu](hpc-cache-add-storage.md). Procedura różni się w zależności od usługi BLOB Storage lub eksportu systemu plików NFS.

Oto kilka porad:

* W przypadku obu typów magazynów należy określić, jak znaleźć system magazynowania zaplecza (adres NFS lub nazwę kontenera obiektów BLOB) oraz ścieżkę przestrzeni nazw dostępną dla klienta.

* Podczas tworzenia obiektu docelowego magazynu obiektów BLOB upewnij się, że pamięć podręczna ma uprawnienia dostępu do konta magazynu, zgodnie z opisem w temacie [Dodawanie ról kontroli dostępu](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account). Jeśli nie masz pewności, że konfiguracja roli zakończy się pomyślnie, najpierw utwórz pamięć podręczną, a następnie ponownie Dodaj magazyn obiektów BLOB.

* Podczas tworzenia miejsca docelowego magazynu NFS należy określić [model użycia](hpc-cache-add-storage.md#choose-a-usage-model). Ustawienie model użycia ułatwia optymalizację przepływu pracy w pamięci podręcznej.

## <a name="add-resource-tags-optional"></a>Dodaj Tagi zasobów (opcjonalnie)

Na stronie **Tagi** można dodać [Tagi zasobów](https://go.microsoft.com/fwlink/?linkid=873112) do wystąpienia pamięci podręcznej platformy Azure HPC.

## <a name="finish-creating-the-cache"></a>Zakończ tworzenie pamięci podręcznej

Po skonfigurowaniu nowej pamięci podręcznej kliknij kartę **Recenzja + tworzenie** . Portal sprawdza poprawność wybranych opcji i pozwala przejrzeć wybrane opcje. Jeśli wszystko jest poprawne, kliknij przycisk **Utwórz**. 

Tworzenie pamięci podręcznej zajmie około 10 minut. Postęp można śledzić w panelu powiadomienia Azure Portal. 

![zrzut ekranu przedstawiający tworzenie stron "wdrażanie" i "powiadomienia" w portalu](media/hpc-cache-deploy-status.png)

Po zakończeniu tworzenia zostanie wyświetlone powiadomienie z linkiem do nowego wystąpienia usługi Azure HPC cache, a pamięć podręczna zostanie wyświetlona na liście **zasobów** subskrypcji. 

![zrzut ekranu wystąpienia pamięci podręcznej platformy Azure HPC w Azure Portal](media/hpc-cache-new-overview.png)

## <a name="next-steps"></a>Następne kroki

Gdy pamięć podręczna zostanie wyświetlona na liście **zasobów** , można ją zainstalować na potrzeby dostępu klienta, użyć jej do przenoszenia danych zestawu roboczego do nowego obiektu docelowego usługi Azure Blob Storage lub definiowania dodatkowych źródeł danych.

* [Instalowanie pamięci podręcznej platformy Azure HPC](hpc-cache-mount.md)
* [Przenoszenie danych do usługi Azure Blob Storage dla pamięci podręcznej platformy Azure HPC](hpc-cache-ingest.md)
* [Dodaj cele magazynu](hpc-cache-add-storage.md)
