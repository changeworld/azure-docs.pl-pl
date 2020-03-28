---
title: 'Samouczek: Dodawanie węzłów do klastra plików usługi Azure FXT Edge'
description: Jak dodać węzły do pamięci podręcznej magazynu usługi Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 6251fe8f88b7db25e3c09898540e07754d72fb0d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551951"
---
# <a name="tutorial-add-cluster-nodes-to-an-azure-fxt-edge-filer-cluster"></a>Samouczek: Dodawanie węzłów klastra do klastra plików usługi Azure FXT Edge

Nowy klaster azure FXT Edge Filer jest tworzony tylko z jednym węzłem. Należy dodać co najmniej dwa węzły i włączyć wysoką dostępność przed wykonaniem innej konfiguracji. 

W tym samouczku wyjaśniono, jak dodać węzły klastra i włączyć funkcję wysokiej dostępności (HA). 

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jak dodać węzły do klastra FXT
> * Jak włączyć ha

Czynności opisane w tym samouczku potrwać około 45 minut.

Przed rozpoczęciem tego samouczka włącz węzły, które chcesz dodać i [ustaw ich początkowe hasła](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Załaduj stronę Węzły klastra

Otwórz Panel sterowania klastra w przeglądarce internetowej i zaloguj się jako administrator. (Szczegółowe instrukcje znajdują się w artykule przeglądowym, w obszarze [Otwórz strony Ustawienia](fxt-cluster-create.md#open-the-settings-pages).)

Po otwarciu panelu sterowania zostanie wyświetle kartę **Pulpit nawigacyjny.** 

![Pulpit nawigacyjny Panelu sterowania (pierwsza karta)](media/fxt-cluster-config/dashboard-1-node.png)

Ten obraz przedstawia pulpit nawigacyjny nowo utworzonego klastra z jednym węzłem.

## <a name="2-locate-the-node-to-add"></a>2. Znajdź węzeł, aby dodać

Aby dodać węzły, kliknij kartę **Ustawienia** i wybierz stronę **Węzły FXT** w sekcji **Klaster.**

![Karta Ustawienia panelu sterowania (druga karta) z załadowanymi węzłami klastra > FXT](media/fxt-cluster-config/settings-fxt-nodes.png)

Lista **węzłów FXT — nieprzyłączone** pokazuje wszystkie nieprzypisane węzły FXT (większość centrów danych ma tylko kilka. Znajdź węzły FXT, które chcesz dodać do klastra.

> [!Tip] 
> Jeśli nie możesz znaleźć żądanego węzła na liście **Nieprzyłączone,** sprawdź, czy spełnia on następujące wymagania:
> 
> * Jest włączony i miał [ustawioną hasło roota](fxt-node-password.md).
> * Jest podłączony do sieci, do której można uzyskać dostęp. Jeśli używasz sieci VLAN, musi ona znajdować się na tej samej sieci VLAN co klaster.
> * Można go wykryć za pomocą protokołu Bonjour. 
>
>   Niektóre ustawienia zapory blokują porty TCP/UDP używane przez Bonjour, co uniemożliwia systemowi operacyjnemu FXT automatyczne wykrywanie węzłów.
> 
> Jeśli węzła, który chcesz dodać, nie ma na liście, wypróbuj następujące rozwiązania: 
> 
> * Kliknij przycisk **Odnajdowanie ręczne,** aby znaleźć go według adresu IP.
> 
> * Ręczne przypisywanie tymczasowych adresów IP. Jest to rzadkie, ale może być konieczne, jeśli używasz oznakowanych sieci VLAN, a węzły nie znajdują się w odpowiedniej sieci lub sieć nie zezwala na samodzielnie przypisane adresy IP. Postępuj zgodnie z instrukcjami w starszej wersji tego dokumentu, aby [ręcznie ustawić statyczny adres IP](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nazwa węzła, adres IP, wersja oprogramowania i stan uprawnień są wyświetlane na liście. Zazwyczaj **kolumna Stan** mówi "Chce dołączyć" lub opisuje problem z systemem lub sprzętem, który sprawia, że węzeł nie kwalifikuje się do przyłączenia się do klastra.

Kolumna **Akcje** zawiera przyciski, które umożliwiają dodanie węzła do klastra lub zaktualizowanie jego oprogramowania. Przycisk aktualizacji automatycznie instaluje wersję oprogramowania, która pasuje do węzłów już w klastrze.

Wszystkie węzły w klastrze muszą używać tej samej wersji systemu operacyjnego, ale nie trzeba aktualizować oprogramowania przed dodaniem węzła. Po kliknięciu przycisku **Zezwalaj na dołączanie** proces dołączania do klastra automatycznie sprawdza i instaluje oprogramowanie systemu operacyjnego zgodne z wersją w klastrze.

Aby dowiedzieć się więcej o opcjach na tej stronie, przeczytaj artykuł [ **Węzły** > FXT klastra](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) w Przewodniku konfiguracji klastra.

## <a name="3-click-the-allow-to-join-button"></a>3. Kliknij przycisk "Zezwalaj na dołączenie" 

Kliknij przycisk **Zezwalaj na dołączenie*** w kolumnie **Akcje** dla węzła, który chcesz dodać.

Po kliknięciu przycisku stan węzła może ulec zmianie, ponieważ jego oprogramowanie jest aktualizowane w ramach przygotowań do dodania go do klastra. 

Poniższy obraz ekspozycyjny przedstawia węzeł, który jest w trakcie dołączania do klastra (najprawdopodobniej otrzymuje aktualizację systemu operacyjnego przed dodaniem). W kolumnie **Akcje** nie są wyświetlane żadne przyciski dla węzłów, które są w trakcie dodawania do klastra.

![jeden wiersz tabeli węzłów, zawierający nazwę węzła, adres IP, wersję oprogramowania, komunikat "Dozwolone dołączenie" i pustą ostatnią kolumnę](media/fxt-cluster-config/node-join-in-process.png)

Po kilku chwilach nowy węzeł powinien pojawić się na liście węzłów klastra u góry strony ustawień **węzłów FXT.** 

Powtórz ten proces, aby dodać inne węzły do klastra. Nie trzeba czekać na jeden węzeł, aby zakończyć dołączanie do klastra przed uruchomieniem innego.

## <a name="enable-high-availability"></a>Włącz wysoką dostępność

Po dodaniu drugiego węzła do klastra na pulpicie nawigacyjnym panelu sterowania może zostać wyświetlony komunikat ostrzegawczy, że funkcja wysokiej dostępności nie jest skonfigurowana. 

Wysoka dostępność (HA) umożliwia węzłom klastra kompensowanie siebie nawzajem, jeśli jeden idzie w dół. Funkcja ha nie jest domyślnie włączona.

![Karta pulpitu nawigacyjnego z komunikatem "Klaster ma więcej niż jeden węzeł, ale usługa ha nie jest włączona ..." w tabeli Warunki](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Nie należy włączać wysokiej dostępności, dopóki w klastrze nie ma co najmniej trzech węzłów.

Wykonaj tę procedurę, aby włączyć ha: 

1. Załaduj stronę **Wysoka dostępność** w sekcji **Klaster** na karcie **Ustawienia.**

   ![Strona konfiguracji usługi ha (cluster > wysoka dostępność). Pole wyboru "Włącz HA" znajduje się u góry, a przycisk prześlij znajduje się na dole.](media/fxt-cluster-config/enable-ha.png)

2. Kliknij pole oznaczone jako **Włącz wysokiej jakości** i kliknij przycisk **Prześlij.** 

Na **pulpicie nawigacyjnym** pojawi się alert potwierdzający, że funkcja ha jest włączona.

![Tabela pulpitu nawigacyjnego z komunikatem "Ha jest teraz w pełni skonfigurowany"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Następne kroki

Po dodaniu wszystkich węzłów w klastrze kontynuuj konfigurację, konfigurując długoterminowe magazynowanie klastra.

> [!div class="nextstepaction"]
> [Dodawanie magazynu zaplecza i konfigurowanie wirtualnego obszaru nazw](fxt-add-storage.md)