---
title: Zarządzanie klastrem Avere vFXT — Azure
description: Jak zarządzać klastrem Avere — dodawanie lub usuwanie węzłów, ponowne uruchamianie, zatrzymywania lub niszczenia klastra vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 94db4a93025b6e3d633368d924e3e0c518d108ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153483"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Zarządzanie klastrem Avere vFXT

W pewnym momencie cyklu życia klastra Avere vFXT for Azure może być konieczne dodanie węzłów klastra lub uruchomienie lub ponowne uruchomienie klastra. Po zakończeniu projektu musisz wiedzieć, jak zatrzymać klaster i usunąć go na stałe.

W tym artykule wyjaśniono, jak dodać lub usunąć węzły klastra i inne podstawowe operacje klastra. Jeśli chcesz zmienić ustawienia klastra lub monitorować jego pracę, użyj [Panelu sterowania Avere](avere-vfxt-cluster-gui.md).

W zależności od zadania zarządzania może być konieczne użycie jednego z trzech różnych narzędzi: Avere Control Panel, vfxt.py skryptu zarządzania klastrem wiersza polecenia i witryny Azure portal.

W tej tabeli przedstawiono przegląd narzędzi, których można używać dla każdego zadania.

| Akcja | Panel sterowania Avere | vfxt.py  | Portal Azure |
| --- | --- | --- | --- |
| Dodawanie węzłów klastra | nie | tak | nie |
| Usuwanie węzłów klastra | tak | nie | nie |
| Zatrzymywać węzeł klastra | tak (można również ponownie uruchomić usługi lub ponownie uruchomić) | nie | wyłączanie węzła maszynowego z portalu jest interpretowane jako awaria węzła |
| Uruchamianie zatrzymanego węzła | nie | nie | tak |
| Niszczenie pojedynczego węzła klastra | nie | nie | tak |
| Ponowne uruchamianie klastra |  |  |  |
| Bezpieczne wyłączenie lub zatrzymanie klastra | tak | tak | nie |
| Zniszcz klaster  | nie | tak | tak, ale integralność danych nie jest gwarantowana |

Szczegółowe instrukcje dla każdego narzędzia znajdują się poniżej.

## <a name="about-stopped-instances-in-azure"></a>Zatrzymane wystąpienia na platformie Azure – informacje

Po zamknięciu lub zatrzymaniu dowolnej maszyny Wirtualnej platformy Azure przestanie ponosić opłaty obliczeniowe, ale nadal musisz zapłacić za jego magazyn. Jeśli zostanie zamknięty węzeł vFXT lub cały klaster vFXT i nie zamierzasz go ponownie uruchomić, należy użyć witryny Azure portal, aby usunąć powiązane maszyny wirtualne.

W witrynie Azure portal *zatrzymanego* węzła (który można ponownie uruchomić) pokazuje stan **zatrzymany** w witrynie Azure portal. *Usunięty* węzeł pokazuje stan **zatrzymany (cofnięty przydział)** i nie ponosi już opłat obliczeniowych lub magazynowych.

Przed usunięciem maszyny Wirtualnej upewnij się, że wszystkie zmienione dane zostały zapisane z pamięci podręcznej do magazynu zaplecza przy użyciu Panelu sterowania Avere lub vfxt.py opcje, aby zatrzymać lub zamknąć klaster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Zarządzanie klastrem za pomocą Panelu sterowania Avere

Do wykonywania następujących zadań można używać Panelu sterowania Avere:

* Zatrzymywać lub ponownie uruchamiać poszczególne węzły
* Usuwanie węzła z klastra
* Zatrzymywanie lub ponowne uruchamianie całego klastra

Avere Panel sterowania nadaje priorytet integralności danych, więc próbuje zapisać wszelkie zmienione dane do magazynu zaplecza przed operacją potencjalnie destrukcyjną. Dzięki temu jest bezpieczniejszą opcją niż witryna Azure portal.

Uzyskaj dostęp do Panelu sterowania Avere z przeglądarki internetowej. Postępuj zgodnie z instrukcjami w [programie Access the vFXT cluster,](avere-vfxt-cluster-gui.md) jeśli potrzebujesz pomocy.

### <a name="manage-nodes-with-avere-control-panel"></a>Zarządzanie węzłami za pomocą Panelu sterowania Avere

Strona ustawień **węzłów FXT** ma kontrolki do zarządzania poszczególnymi węzłami.

Aby zamknąć, ponownie uruchomić lub usunąć węzeł, znajdź węzeł na liście na stronie **Węzły FXT** i kliknij odpowiedni przycisk w kolumnie **Akcje.**

> [!NOTE]
> Adresy IP mogą poruszać się między węzłami klastra po zmianie liczby aktywnych węzłów.

Więcej informacji można znaleźć w przewodniku po [ustawieniach klastra](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) Avere > węzłów klastra.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zatrzymywać lub ponownie uruchamiać klaster za pomocą Panelu sterowania Avere

Strona Ustawienia **konserwacji systemu** zawiera polecenia służące do ponownego uruchamiania usług klastra, ponownego uruchamiania klastra lub bezpiecznego wyłączania klastra. Szczegółowe informacje można znaleźć w [podręczniku Administracja > konserwacji systemu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (w przewodniku po ustawieniach klastra Avere).

Gdy klaster zaczyna się zamykać, księguje wiadomości o stanie na karcie **Pulpit nawigacyjny.** Po kilku chwilach wiadomości zatrzymać i ostatecznie sesji Panelu sterowania Avere przestaje odpowiadać, co oznacza, że klaster został zamknięty.

## <a name="manage-the-cluster-with-vfxtpy"></a>Zarządzanie klastrem za pomocą vfxt.py

vfxt.py jest narzędziem wiersza polecenia do tworzenia klastra i zarządzania klastrem.

vfxt.py jest preinstalowany na maszynie wirtualnej kontrolera klastra. Jeśli chcesz zainstalować go w innym systemie, <https://github.com/Azure/AvereSDK>zapoznaj się z dokumentacją w .

Skrypt vfxt.py może być używany do tych zadań zarządzania klastrem:

* Dodawanie nowych węzłów do klastra
* Zatrzymywać lub uruchamiać klaster  
* Niszcz klaster

Podobnie jak Avere Control Panel, vfxt.py operacje próbują upewnić się, że zmienione dane są przechowywane na stałe w magazynie zaplecza przed zamknięciem lub zniszczeniem klastra lub węzła. Dzięki temu jest bezpieczniejszą opcją niż witryna Azure portal.

Pełny przewodnik użycia vfxt.py jest dostępny w usłudze GitHub: [Zarządzanie klastrami w chmurze z vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Dodawanie węzłów klastra za pomocą vfxt.py

Przykładowy skrypt polecenia dodawania węzłów klastra znajduje się na kontrolerze klastra. Znajdź ``./add-nodes`` kontroler i otwórz go w edytorze, aby dostosować go za pomocą informacji klastra.

Klaster musi być uruchomiony, aby użyć tego polecenia.

Podać następujące wartości:

* Nazwa grupy zasobów dla klastra, a także dla zasobów sieciowych i magazynowych, jeśli nie znajdują się w tej samej grupie zasobów co klaster
* Lokalizacja klastra
* Sieć klastrów i podsieć
* Rola dostępu do węzła klastra (użyj wbudowanej roli [Avere Operator)](../role-based-access-control/built-in-roles.md#avere-operator)
* Adres IP zarządzania klastrem i hasło administracyjne
* Liczba węzłów do dodania (1, 2 lub 3)
* Typ wystąpienia węzła i wartości rozmiaru pamięci podręcznej

Jeśli prototyp nie jest używany, należy utworzyć polecenie podobne do poniższych, w tym wszystkie informacje opisane powyżej.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Aby uzyskać więcej informacji, przeczytaj artykuł [Dodawanie węzłów do klastra](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) w przewodniku użycia vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zatrzymywać klaster z vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Uruchom zatrzymaną klaster z vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Ponieważ klaster jest zatrzymany, należy przekazać identyfikatory wystąpień, aby określić węzły klastra. Przeczytaj [Określanie klastra, który ma być modyfikowany](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) w przewodniku użycia vfxt.py, aby dowiedzieć się więcej.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zniszcz klaster za pomocą vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Opcji ``--quick-destroy`` można użyć, jeśli nie chcesz zapisywać zmienionych danych z pamięci podręcznej klastra.

Zapoznaj się z [vfxt.py podręcznikiem użytkowania, aby](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) uzyskać dodatkowe informacje.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Zarządzanie maszynami wirtualnymi klastra z witryny Azure portal

Witryna Azure portal może służyć do niszczenia maszyn wirtualnych klastra indywidualnie, ale integralność danych nie jest gwarantowana, jeśli klaster nie jest najpierw zamknięty czysto.

Witryna Azure portal może służyć do tych zadań zarządzania klastrem:

* Uruchamianie zatrzymanego węzła vFXT
* Zatrzymaj pojedynczy węzeł vFXT (klaster interpretuje to jako błąd węzła)
* Zniszcz klaster vFXT, *jeśli* nie trzeba się upewnić, że zmienione dane w pamięci podręcznej klastra są zapisywane na głównym filer
* Trwale usuwaj węzły vFXT i inne zasoby klastra po ich bezpiecznym zamknięciu

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Ponowne uruchamianie wystąpień vFXT z witryny Azure portal

Jeśli musisz ponownie uruchomić zatrzymany węzeł, należy użyć witryny Azure portal. Wybierz **opcję Maszyny wirtualne** w menu po lewej stronie, a następnie kliknij nazwę maszyny wirtualnej na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Start** u góry strony przeglądu, aby ponownie uaktywnić maszynę wirtualną.

![Ekran portalu Azure z opcją uruchomienia zatrzymanej maszyny wirtualnej](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Usuwanie węzłów klastra

Aby usunąć jeden węzeł z klastra vFXT, ale zachować pozostałą część klastra, należy najpierw [usunąć węzeł z klastra](#manage-nodes-with-avere-control-panel) za pomocą Panelu sterowania Avere.

> [!CAUTION]
> Jeśli usuniesz węzeł bez uprzedniego usunięcia go z klastra vFXT, dane mogą zostać utracone.

Aby trwale zniszczyć jedno lub więcej wystąpień używanych jako węzeł vFXT, użyj witryny Azure portal.
Wybierz **opcję Maszyny wirtualne** w menu po lewej stronie, a następnie kliknij nazwę maszyny wirtualnej na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Usuń** w górnej części strony przeglądu, aby trwale zniszczyć maszynę wirtualną.

Za pomocą tej metody można trwale usunąć węzły klastra po ich bezpiecznym zamknięciu.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Niszcz klaster z witryny Azure portal

> [!NOTE]
> Jeśli chcesz, aby wszystkie pozostałe zmiany klienta w pamięci podręcznej zostały zapisane w `--destroy` magazynie zaplecza, użyj opcji vfxt.py lub użyj Panelu sterowania Avere, aby zamknąć klaster czysto przed usunięciem wystąpień węzłów w witrynie Azure portal.

Wystąpienia węzłów można trwale zniszczyć, usuwając je w witrynie Azure portal. Można je usuwać po kolei, jak opisano powyżej, lub za pomocą strony **Maszyny wirtualne,** aby znaleźć wszystkie maszyny wirtualne klastra, zaznaczyć je za pomocą pól wyboru i kliknąć przycisk **Usuń,** aby usunąć je wszystkie w jednej akcji.

![Lista maszyn wirtualnych w portalu, filtrowane według terminu "klaster", z trzema z czterech zaznaczonych i wyróżnionych](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Usuwanie dodatkowych zasobów klastra z witryny Azure Portal

Jeśli utworzono dodatkowe zasoby specjalnie dla klastra vFXT, można usunąć je w ramach rozbiórki klastra. Nie należy niszczyć elementów, które zawierają potrzebne dane lub żadnych elementów, które są udostępniane innym projektom.

Oprócz usuwania węzłów klastra należy rozważyć usunięcie tych składników:

* Maszyna wirtualna kontrolera klastra
* Dyski danych skojarzone z węzłami klastra
* Interfejsy sieciowe i publiczne adresy IP skojarzone ze składnikami klastra
* Sieci wirtualne
* Kontenery magazynowe i konta magazynu **(tylko** wtedy, gdy nie zawierają ważnych danych)
* Zestaw dostępności

![Lista "wszystkie zasoby" portalu Azure z zasobami utworzonymi dla klastra testowego](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Usuwanie grupy zasobów klastra z witryny Azure Portal

Jeśli grupa zasobów została utworzona specjalnie w celu umieszczenia klastra, można zniszczyć wszystkie powiązane zasoby dla klastra, niszcząc grupę zasobów.

> [!Caution]
> Zniszczyć grupę zasobów tylko wtedy, gdy masz pewność, że nic wartości znajduje się w grupie. Na przykład upewnij się, że zostały przeniesione wszystkie potrzebne dane z kontenerów magazynu w grupie zasobów.  

Aby usunąć grupę zasobów, kliknij pozycję **Grupy zasobów** w lewym menu portalu i przefiltruj listę grup zasobów, aby znaleźć grupę utworzonej dla klastra vFXT. Zaznacz grupę zasobów i kliknij trzy kropki po prawej stronie panelu. Wybierz pozycję **Usuń grupę zasobów**. Portal poprosi cię o potwierdzenie usunięcia, które jest nieodwracalne.

![Grupa zasobów z akcji "Usuń grupę zasobów"](media/avere-vfxt-delete-resource-group.png)
