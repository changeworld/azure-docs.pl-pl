---
title: Zarządzanie klastrem vFXT Avere - Azure
description: Zarządzanie klastrem Avere — Dodawanie lub usuwanie węzłów, ponowne uruchomienie, Zatrzymaj i zniszczyć klaster vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: be9205fdf7fec0661d7382ed0d1bedf47487b15e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282206"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Zarządzanie klastrem Avere vFXT

Po utworzeniu klastra, konieczne może być dodawanie węzłów klastra, zatrzymywanie lub ponowne uruchomienie klastra. A po zakończeniu projektu, trzeba znać sposób zatrzymywania i trwale usunąć klaster. 

W zależności od zadania zarządzania klastrem może być konieczne użycie Avere Panelu sterowania, skrypt tworzenia klastra vfxt.py wiersza polecenia lub witryny Azure portal, aby to zrobić. 

Ta tabela zawiera omówienie z których narzędzi może służyć do każdego zadania. 

| Akcja | Panel sterowania Avere | vfxt.PY  | Azure Portal |
| --- | --- | --- | --- |
| Dodawanie węzłów klastra | nie | tak | nie |
| Usuwanie węzłów klastra | tak | nie | nie |
| Zatrzymanie węzła klastra | Tak (można również uruchomić ponownie usługi lub ponowne uruchomienie) | nie | wyłączania węzeł maszyny Wirtualnej z poziomu portalu jest interpretowany jako awarii węzła |
| Rozpocznij zatrzymania węzła | nie | nie | tak |
| Zniszcz jednego węzła klastra | nie | nie | tak |
| Ponowne uruchomienie klastra |  |  |  |
| Zamknij lub Zatrzymaj klaster bezpiecznie | tak | tak | nie |
| Zniszczyć klaster  | nie | tak | tak, ale nie ma żadnej gwarancji spójności danych |

Poniżej znajdują się szczegółowe instrukcje dotyczące każdego z narzędzi.

## <a name="about-stopped-instances-in-azure"></a>O wystąpieniach zatrzymania na platformie Azure

Zamknij lub Zatrzymaj wszystkie maszyny Wirtualnej platformy Azure, przestanie być naliczane opłaty za zasoby obliczeniowe, ale nadal musisz zapłacić za jego magazynu. Jeśli nie zamierzasz uruchomić go ponownie zostanie zamknięta węzła vFXT lub vFXT całego klastra, należy używać witryny Azure portal, można usunąć powiązane maszyn wirtualnych. 

W witrynie Azure portal *zatrzymana* węzeł (która może zostać uruchomiony ponownie) Wyświetla stan **zatrzymana** w witrynie Azure portal; *usunięte* węzeł Wyświetla stan **zatrzymana (cofnięty przydział)**  i go już spowoduje naliczenie opłaty za obliczenia oraz magazynowanie.

Przed usunięciem maszyny Wirtualnej, upewnij się, że wszystkie dane zmienione został zapisany z pamięci podręcznej magazynu zaplecza za pomocą opcji Avere Panelu sterowania lub vfxt.py, aby zatrzymać lub zamknąć klaster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Zarządzanie klastrem przy użyciu Panelu sterowania Avere 

Panel sterowania Avere może służyć do wykonywania tych zadań: 

* Zatrzymywanie lub ponowne uruchomienie pojedynczych węzłów
* Usuń węzeł z klastra
* Zatrzymywanie lub ponowne uruchomienie całego klastra

Panel sterowania Avere priorytetem integralność danych, aby podejmował próbę zapisu zmienionych danych związanych z magazynem wewnętrznej bazy danych przed wykonaniem operacji prawdopodobnie destrukcyjne. Dzięki temu bezpieczniejsze niż Avere portal. 

Panel sterowania Avere dostęp z przeglądarki sieci web. Postępuj zgodnie z instrukcjami w [dostęp do klastra vFXT](avere-vfxt-cluster-gui.md) Jeśli potrzebujesz pomocy.

### <a name="manage-nodes-with-avere-control-panel"></a>Zarządzaj węzłami za pomocą Panelu sterowania Avere

**Węzłów FXT** strony ustawień zawiera też kontrolki służące do zarządzania poszczególne węzły.

Do zamknięcia, ponownie uruchomić lub usunąć węzeł, Znajdź węzeł na liście na **węzłów FXT** strony, a następnie kliknij odpowiedni przycisk w jego **akcje** kolumny.

> [!NOTE] 
> Adresy IP może poruszać się między węzłami klastra, po zmianie liczba aktywnych węzłów.

Odczyt [klaster > węzły FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) w przewodniku ustawienia Avere klastra, aby uzyskać więcej informacji.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Zatrzymaj lub uruchom ponownie klaster przy użyciu Panelu sterowania Avere

**Konserwacji systemu** strona ustawień zawiera polecenia służące do ponownego uruchamiania usług klastrowania, ponownego uruchamiania klastra lub bezpiecznie wyłączania klastra. Odczyt [Administracja > konserwacji systemu](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (Avere klastra ustawienia przewodnik) Aby uzyskać szczegółowe informacje.

Gdy klaster jest zamykana, publikuje komunikaty o stanie do **pulpit nawigacyjny** kartę na początku. Po kilku chwilach sesji Panelu sterowania Avere przestanie odpowiadać, co oznacza, że klaster został zamknięty.

## <a name="manage-the-cluster-with-vfxtpy"></a>Zarządzanie klastrem przy użyciu vfxt.py

vfxt.PY jest narzędziem wiersza polecenia do tworzenia klastra i zarządzania. 

vfxt.PY jest preinstalowany na kontrolerze klastra maszyny Wirtualnej. Jeśli chcesz zainstalować je na innym systemie, zapoznaj się z dokumentacją w <https://github.com/Azure/AvereSDK>.

Skrypt vfxt.py może służyć do tych zadań zarządzania klastrem:

* Dodawanie nowych węzłów do klastra
* Zatrzymanie lub uruchomienie klastra  
* Zniszczyć klaster

Podobnie jak Avere Panelu sterowania operacje vfxt.py spróbuj upewnij się, że zmienione dane są przechowywane trwale magazynu zaplecza przed zamknięciem lub niszczenie klastra lub węzła. Dzięki temu bezpieczniejsze niż Avere portal.

Podręcznik użycia pełną vfxt.py jest dostępna w witrynie GitHub: [Klaster zarządzania chmurą vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Dodawanie węzłów klastra z vfxt.py

Przykładowy skrypt poleceń dodawania węzłów klastra znajduje się na kontrolerze klastra. Znajdź ``./add-nodes`` na kontrolerze, a następnie otwórz go w edytorze, aby dostosować ją przy użyciu informacji klastra. 

Klaster musi działać, aby użyć tego polecenia. 

Podaj następujące wartości: 

* Nazwa grupy zasobów klastra, a także dla zasobów magazynu i sieci, jeśli nie są takie same jak klastra
* Lokalizacja klastra
* Sieć klastra i podsieci 
* Rola dostępu węzła klastra (za pomocą wbudowanej roli [Avere Operator](../role-based-access-control/built-in-roles.md#avere-operator))
* Adres IP zarządzania klastra i hasło administracyjne 
* Liczba węzłów, aby dodać (1, 2 lub 3)
* Wystąpienie typu i pamięć podręczna rozmiar wartości węzła 

Jeśli nie używasz prototypu, należy tworzyć polecenie podobne do następujących, w tym wszystkie informacje opisane powyżej. 

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

Aby uzyskać więcej informacji, przeczytaj [dodać węzły do klastra](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) w przewodniku użytkowania vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Zatrzymaj klaster z vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Uruchomienie klastra zatrzymana za pomocą vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Ponieważ klaster zostanie zatrzymana, należy przekazać identyfikatory wystąpień, aby określić węzłów klastra. Odczyt [Określanie klastra, które można modyfikować](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) w przewodniku użytkowania vfxt.py, aby dowiedzieć się więcej.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Zniszcz klaster przy użyciu vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

Opcja ``--quick-destroy`` można użyć, jeśli nie chcesz zapisać zmienione dane z pamięci podręcznej klastra.

Odczyt [Podręcznik użycia vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) Aby uzyskać dodatkowe informacje.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Zarządzanie maszynami wirtualnymi klastra w witrynie Azure portal 

Witryny Azure portal może służyć do indywidualnie zniszczyć klaster maszyn wirtualnych, ale nie jest gwarantowana integralność danych, jeśli klaster nie jest zamykane jak pierwsze prawidłowo zamykaj wystąpienia. 

Witryna Azure portal może służyć do tych zadań zarządzania klastrem: 

* Uruchomienia węzła vFXT zatrzymania
* Zatrzymaj węzeł poszczególnych vFXT (klaster interpretuje jako awarii węzła)
* Zniszcz klaster vFXT *Jeśli* nie należy się upewnić, że zmienione dane w pamięci podręcznej klastra są zapisywane filtr core
* Trwale usunąć węzły vFXT i innych zasobów klastra po one zostać zamknięty bezpiecznie

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Ponowne uruchamianie wystąpień vFXT w witrynie Azure portal

Jeśli zachodzi potrzeba ponownego uruchomienia węzła zatrzymana, należy użyć witryny Azure portal. Wybierz **maszyn wirtualnych** w menu po lewej stronie, a następnie kliknij nazwę maszyny Wirtualnej, na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Start** znajdujący się u górnej części strony Przegląd, aby ponownie aktywować maszyny Wirtualnej.

![Ekranie za pomocą portalu Azure przedstawiający opcji uruchamiania zatrzymanej maszyny wirtualnej](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Usuwanie węzłów klastra

Jeśli chcesz usunąć jeden węzeł z klastra vFXT, zachowując pozostałą część klastra, należy najpierw [Usuń węzeł z klastra](#manage-nodes-with-avere-control-panel) Avere w Panelu sterowania.

> [!CAUTION]
> Usunięcie węzła bez usuwania go z klastra vFXT, dane mogą zostać utracone.

Aby trwale usunąć co najmniej jedno wystąpienie, używane jako węzeł vFXT, użyj witryny Azure portal.
Wybierz **maszyn wirtualnych** w menu po lewej stronie, a następnie kliknij nazwę maszyny Wirtualnej, na liście, aby otworzyć jej stronę przeglądu.

Kliknij przycisk **Usuń** przycisk w górnej części strony Przegląd, aby trwale usunąć maszynę Wirtualną.

Można użyć tej metody, aby trwale usunąć węzły klastra, po ich zostać zamknięty bezpiecznie. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Zniszcz klaster w witrynie Azure portal

> [!NOTE] 
> Jeśli chcesz, aby wszelkie pozostałe zmiany klienta w pamięci podręcznej w celu zapisania magazynu zaplecza, użyj vfxt.py `--destroy` opcję lub w trybie awaryjnym klastra przed usunięciem wystąpienia węzła w witrynie Azure portal za pomocą Panelu sterowania Avere.

Wystąpienia węzła może zniszczyć trwale, usuwając je w witrynie Azure portal. Możesz je pojedynczo zgodnie z opisem powyżej, można usunąć **maszyn wirtualnych** strony, aby znaleźć wszystkie maszyn wirtualnych klastra, zaznacz je przy użyciu pola wyboru, a następnie kliknij przycisk **Usuń** przycisk, aby je usunąć w jedna akcja.

![Lista maszyn wirtualnych w portalu, przefiltrowane przez określenie "klastra", z których trzy z czterech zaznaczone i wyróżnione](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Usunięcie klastra dodatkowych zasobów w witrynie Azure portal

Jeśli utworzono dodatkowe zasoby dla klastra vFXT, można usunąć je jako część zniszczenia klastra. Niszczy elementów, które zawierają dane, których potrzebujesz, lub wszystkie elementy, które są współużytkowane z innymi projektami.

Oprócz usuwania węzłów klastra, rozważ usunięcie tych składników: 

* Kontroler klastra maszyny Wirtualnej
* Dysków danych skojarzonych z węzłów klastra
* Interfejsy sieciowe i publiczne adresy IP skojarzone ze składnikami klastra
* Sieci wirtualne
* Konta magazynu (**tylko** jeśli zawierają one nie ważnych danych)
* Zestaw dostępności 

![Witryna Azure portal listy "wszystkie zasoby" przedstawiający zasoby tworzone dla klastra testowego](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Usuń grupę zasobów klastra w witrynie Azure portal

Jeśli utworzono grupę zasobów, specjalnie w celu przechowywania klastra może zniszczyć wszystkie powiązane zasoby dla klastra, poprzez zniszczenie grupy zasobów. 

> [!Caution] 
> Grupa zasobów należy zniszczyć tylko, jeśli masz pewność, nothing wartość znajduje się w grupie. Na przykład upewnij się, że wszelkie potrzebne dane zostały przeniesione z wszystkie kontenery magazynu w grupie zasobów.  

Aby usunąć grupę zasobów, kliknij przycisk **grup zasobów** w menu po lewej stronie portalu i filtrowania listy grup zasobów, aby znaleźć utworzonym dla klastra vFXT. Wybierz grupę zasobów, a następnie kliknij przycisk z wielokropkiem w prawym panelu. Wybierz pozycję **Usuń grupę zasobów**. Portal wyświetli monit o potwierdzenie usunięcia, w której jest nieodwracalne.  

![Grupa zasobów Akcja "Usuń grupę zasobów"](media/avere-vfxt-delete-resource-group.png)
