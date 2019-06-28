---
title: Konfiguracja klastra filtr Edge FXT Azure Microsoft — Dodawanie węzłów
description: Jak dodać węzły do pamięci podręcznej z magazynu Azure FXT krawędzi filtr
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 970639eec8c16540d8d7653f1d8bb01e4a397080
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450499"
---
# <a name="tutorial-add-cluster-nodes"></a>Samouczek: Dodawanie węzłów klastra 

Nowy klaster filtr Edge FXT Azure jest tworzony z tylko jednym węzłem. Należy dodać co najmniej dwa dodatkowe węzły, a następnie włączyć wysoką dostępność, przed wykonaniem innych konfiguracji. 

W tym samouczku opisano sposób dodawania węzłów klastra i włączanie funkcji wysokiej dostępności (HA). 

Z tego samouczka dowiesz się: 

> [!div class="checklist"]
> * Jak dodać węzły do klastra FXT
> * Jak włączyć wysokiej dostępności

Kroki opisane w tym samouczku zająć około 45 minut.

Przed rozpoczęciem tego samouczka, Włącz węzły, które chcesz dodać i [ich hasła początkowego](fxt-node-password.md). 

## <a name="1-load-the-cluster-nodes-page"></a>1. Ładowanie strony węzłów klastra

Otwórz Panel sterowania klastra w przeglądarce sieci web, a następnie zaloguj się jako administrator. (Szczegółowe instrukcje znajdują się w artykule omówienie, w obszarze [otwarcie strony ustawień](fxt-cluster-create.md#open-the-settings-pages).)

W Panelu sterowania przedstawiono **pulpit nawigacyjny** karcie po jego otwarciu. 

![Pulpit nawigacyjny w Panelu sterowania (najpierw tabulacji)](media/fxt-cluster-config/dashboard-1-node.png)

Ta ilustracja przedstawia pulpitu nawigacyjnego nowo utworzony klaster z jednym węzłem.

## <a name="2-locate-the-node-to-add"></a>2. Odszukaj węzeł, aby dodać

Aby dodać węzły, kliknij przycisk **ustawienia** kartę i wybierz polecenie **węzłów FXT** strony w **klastra** sekcji.

![Ustawienia Panelu sterowania karcie (druga karta) z klastrem > węzły FXT załadowany](media/fxt-cluster-config/settings-fxt-nodes.png)

**Węzłów FXT — odłączyć** listy węzłów FXT Wyświetla wszystkie nieprzypisane (większość centrów danych ma tylko kilka. Znajdź węzły FXT, które chcesz dodać do klastra.

> [!Tip] 
> Jeśli nie można odnaleźć węzła chcesz na **Unjoined** listy, należy sprawdzić, czy spełnia następujące wymagania:
> 
> * Zostanie włączone i miał [główny ustawiania haseł](fxt-node-password.md).
> * Jest połączona z siecią, w których masz dostęp. Jeśli używasz sieci VLAN, musi być w tej samej sieci VLAN co klaster.
> * Może zostać wykryte przy użyciu protokołu usługi Bonjour. 
>
>   Niektóre ustawienia zapory blokować porty TCP/UDP, używane przez usługi Bonjour, co uniemożliwi systemowi operacyjnemu FXT automatycznie wykryć węzłów.
> 
> Jeśli węzeł, który chcesz dodać, nie ma na liście, wypróbuj następujące rozwiązania: 
> 
> * Kliknij przycisk **odnajdywania ręcznego** przycisk, aby je znaleźć przy użyciu adresu IP.
> 
> * Ręczne przypisywanie adresów IP tymczasowych. To jest rzadkie, ale mogą być wymagane, jeśli używasz sieci VLAN oznakowane i węzły nie znajdują się na odpowiedniej sieci lub sieci nie zezwala na własnym przypisanych adresów IP. Postępuj zgodnie z instrukcjami w starszej wersji tego dokumentu, aby [ręcznie ustawić statyczny adres IP](https://azure.github.io/Avere/legacy/create_cluster/4_8/html/static_ip.html).

Nazwa węzła, adres IP, wersja oprogramowania i stanu uprawnień są wyświetlane na liście. Zazwyczaj **stan** kolumny albo jest wyświetlany komunikat "Chce dołączyć do" lub w tym artykule opisano problem systemu lub sprzętu, który sprawia, że węzeł nie kwalifikuje się do dołączenia do klastra.

**Akcje** kolumna zawiera przyciski umożliwiające Dodaj węzeł do klastra lub zaktualizować swoje oprogramowanie. Przycisk Aktualizuj automatycznie instaluje wersję oprogramowania, która jest zgodna węzłów w klastrze.

Wszystkie węzły w klastrze, należy użyć tej samej wersji systemu operacyjnego, ale nie jest potrzebny do aktualizacji oprogramowania przed dodaniem węzła. Po kliknięciu **Zezwalaj na sprzężenie** przycisku procesu dołączania klastra automatycznie sprawdza i instaluje oprogramowanie systemu operacyjnego, która jest zgodna z wersją w klastrze.

Aby dowiedzieć się więcej na temat opcji na tej stronie, przeczytaj [ **klastra** > **węzłów FXT** ](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html) w przewodniku po konfiguracji klastra.

## <a name="3-click-the-allow-to-join-button"></a>3. Kliknij przycisk "Zezwalaj na Join" 

Kliknij przycisk **Zezwalaj Dołącz*** znajdujący się w **akcje** kolumny węzła, który chcesz dodać.

Po kliknięciu przycisku, stan węzła mogą ulec zmianie, zgodnie z jego oprogramowanie jest aktualizowane w ramach przygotowania do dodawania go do klastra. 

Na poniższym obrazie przedstawiono węzła, który jest w trakcie procesu przyłączania do klastra (najprawdopodobniej, stają się coraz aktualizacji systemu operacyjnego, przed dodaniem). Przyciski nie są wyświetlane w **akcje** kolumny dla węzłów, które są właśnie dodawany do klastra.

![jeden wiersz w tabeli węzła, przedstawiający nazwę węzła, adres IP, wersja oprogramowania, komunikat "Dozwolone do dołączenia do" i puste ostatniej kolumnie](media/fxt-cluster-config/node-join-in-process.png)

Po kilku chwilach nowego węzła powinna występować na liście węzłów klastra w górnej części **węzłów FXT** strona ustawień. 

Powtórz tę procedurę, aby dodać inne węzły do klastra. Nie trzeba czekać na jeden węzeł zakończyć, dołączenie do klastra przed uruchomieniem innego.

## <a name="enable-high-availability"></a>Włączanie wysokiej dostępności

Po dodaniu drugiego węzła do klastra, może pojawić się komunikat ostrzegawczy, w Panelu sterowania pulpitu nawigacyjnego, który funkcji wysokiej dostępności nie jest skonfigurowane. 

Wysoka dostępność (HA) umożliwia węzłów klastra w celu kompensacji dla siebie, jeśli ulegnie awarii jednej. Wysokiej dostępności nie jest włączona domyślnie.

![Karta pulpitu nawigacyjnego z komunikatem "klaster ma więcej niż jeden węzeł, ale o wysokiej dostępności nie jest włączone..." w tabeli warunków](media/fxt-cluster-config/no-ha-2-nodes.png)

> [!Note] 
> Nie należy włączać wysokiej dostępności, dopóki nie uzyskasz co najmniej trzy węzły w klastrze.

Wykonaj poniższą procedurę, aby włączyć funkcję wysokiej dostępności: 

1. Obciążenia **wysokiej dostępności** strony w **klastra** części **ustawienia** kartę.

   ![Strona konfiguracji o wysokiej dostępności (klastrowania > wysokiej dostępności). Pole wyboru "Włącz HA" znajduje się na górze i przycisk Prześlij znajduje się na dole.](media/fxt-cluster-config/enable-ha.png)

2. Kliknij pole wyboru o nazwie **włączenia zaświadczanie o kondycji** i kliknij przycisk **przesyłania** przycisku. 

Zostanie wyświetlony alert na **pulpit nawigacyjny** aby upewnić się, że włączono wysokiej dostępności.

![Pulpit nawigacyjny tabeli przedstawiający komunikat "HA jest teraz całkowicie skonfigurowałeś"](media/fxt-cluster-config/ha-configured-alert.png)


## <a name="next-steps"></a>Kolejne kroki

Po dodaniu wszystkich węzłów w klastrze, należy kontynuować instalację, konfigurując klastra długoterminowego przechowywania.

> [!div class="nextstepaction"]
> [Dodawanie magazynu zaplecza i konfigurowanie wirtualnej przestrzeni nazw](fxt-add-storage.md)