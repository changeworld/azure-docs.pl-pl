---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419998"
---
Azure Virtual Machines (VMs) przechodzą przez różne stany, które można podzielić na *aprowizacji* i *power* stanów. Celem tego artykułu jest do opisywania tych stanów i specjalnie Podświetl, gdy klienci są rozliczane na przykład użycia. 

## <a name="power-states"></a>Stany zasilania

Stan zasilania reprezentuje ostatni znany stan maszyny Wirtualnej.

![Diagram stanu zasilania maszyny Wirtualnej](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Poniższa tabela zawiera opis każdego wystąpienie przechodzi w stan i wskazuje, czy jest ona rozliczana za użycie wystąpienia, lub nie.

<table>
<tr>
<th>
Stan
</th>
<th>
Opis
</th>
<th>
Rozliczenia użycia wystąpienia
</th>
</tr>
<tr>
<td>
<p><b>Uruchamianie</b></p>
</td>
<td>
<p>Maszyna wirtualna jest uruchamiany.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Nie zostaną obciążeni opłatami</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Uruchomiono</b></p>
</td>
<td>
<p>Normalny stan pracy maszyny wirtualnej</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zatrzymywanie</b></p>
</td>
<td>
<p>Jest to stan przejściowy. Po ukończeniu będzie widoczny jako **zatrzymane**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zatrzymana</b></p>
</td>
<td>
<p>Zamknij system maszynę Wirtualną z w dół w ramach systemu operacyjnego gościa lub przy użyciu interfejsów API wyłączonego zasilania.</p>
<p>Sprzęt wciąż jest przydzielony do maszyny Wirtualnej i pozostaje na hoście. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Rozliczane&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofanie przydziału</b></p>
</td>
<td>
<p>Stan przejściowy. Po zakończeniu maszyny Wirtualnej będzie widoczny jako **alokacji**.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nie zostaną obciążeni opłatami&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofnięto alokację</b></p>
</td>
<td>
<p>Maszyna wirtualna została pomyślnie zatrzymana i usunięte z hosta. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nie zostaną obciążeni opłatami</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Zasobów platformy Azure, takich jak dyski i sieci, są naliczane opłaty. Licencje na oprogramowanie w wystąpieniu nie są naliczane opłaty.

## <a name="provisioning-states"></a>Stany aprowizowania

Stan aprowizacji to stan zainicjowanego przez użytkownika, płaszczyznę kontroli operacji na maszynie Wirtualnej. Te stany są niezależne od jego stanu zasilania maszyny Wirtualnej.

- **Utwórz** — tworzenie maszyny Wirtualnej.

- **Aktualizacja** — aktualizuje model dla istniejącej maszyny Wirtualnej. Niektóre niezawierająca modelu zmienia się do maszyny Wirtualnej, takich jak początek/Restart również podlega aktualizacji.

- **Usuń** — usunięcie maszyny Wirtualnej.

- **Cofnij Przydział** — gdzie zatrzymane i usunięte z hosta maszyny Wirtualnej. Cofanie przydziału maszyny Wirtualnej jest uznawana za aktualizację, będzie on wyświetlany stanach aprowizacji dotyczących aktualizowania.



Poniżej przedstawiono stanach operacja przejściowy, po platformie zaakceptował to działanie inicjowane przez użytkownika:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Stany</b></p>
</td>
<td width="366">
<p>Opis</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Tworzenie</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Aktualizowanie</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Usuwanie</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Stany aprowizowania systemu operacyjnego</b></p>
</td>
<td width="366">
<p>Jeśli Maszynę wirtualną z obrazu systemu operacyjnego, a nie z wyspecjalizowanego obrazu, następujące podstany można zaobserwować:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; maszyna wirtualna jest uruchomiona, a instalacja systemu operacyjnego gościa jest w toku. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; krótkotrwałe stanu. Maszyna wirtualna szybkiego przechodzi do **Powodzenie** chyba, że wszystkie rozszerzenia, które muszą zostać zainstalowane. Instalowanie rozszerzeń może potrwać. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Uwaga</b>: Aprowizacja systemu operacyjnego można przejść do **niepowodzenie** Jeśli wystąpił błąd systemu operacyjnego lub systemu operacyjnego nie można zainstalować w czasie. Klienci, jest naliczana dla maszyny Wirtualnej wdrożonej na infrastrukturze.</p>
</td>
</tr>
</table>


Po zakończeniu operacji maszyny Wirtualnej spowoduje przejście do jednej z następujących stanów:

- **Pomyślnie** — akcje inicjowane przez użytkownika została ukończona.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Nie powiodło się** — reprezentuje operację zakończoną niepowodzeniem. Zapoznaj się z kodów błędów, aby uzyskać więcej informacji i możliwe rozwiązania.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>Widok wystąpienia maszyny Wirtualnej

Widok wystąpienia interfejsu API zawiera informacje o stan uruchomienia maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz [Virtual Machines — widok wystąpienia](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) dokumentacji interfejsu API.

Eksplorator zasobów Azure udostępnia prosty interfejs użytkownika do wyświetlania stanu działania maszyny Wirtualnej: [Eksplorator zasobów](https://resources.azure.com/).

Stany aprowizowania są widoczne właściwości maszyny Wirtualnej i widok wystąpienia. Stany zasilania są dostępne w widoku wystąpienia maszyny wirtualnej. 

