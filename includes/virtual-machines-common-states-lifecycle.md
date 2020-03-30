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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183170"
---
Maszyny wirtualne platformy Azure (VM) przechodzą przez różne stany, które można podzielić na stany *inicjowania obsługi administracyjnej* i *zasilania.* Celem tego artykułu jest opisanie tych stanów, a w szczególności wyróżnienie, gdy klienci są rozliczane na przykład użycie. 

## <a name="power-states"></a>Stany zasilania

Stan zasilania reprezentuje ostatni znany stan maszyny Wirtualnej.

![Diagram stanu zasilania maszyny Wirtualnej](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Poniższa tabela zawiera opis każdego stanu wystąpienia i wskazuje, czy jest rozliczane za użycie wystąpienia, czy nie.

<table>
<tr>
<th>
Stan
</th>
<th>
Opis
</th>
<th>
Rozliczanie użycia wystąpienia
</th>
</tr>
<tr>
<td>
<p><b>Zaczynając</b></p>
</td>
<td>
<p>Maszyna wirtualna jest uruchamiana.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Niezaliczone rachunki</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Uruchomiono</b></p>
</td>
<td>
<p>Normalny stan roboczy maszyny Wirtualnej</p>
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
<p>Jest to stan przejściowy. Po zakończeniu zostanie wyświetlona jako **Zatrzymana**.</p>
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
<p><b>Zatrzymano</b></p>
</td>
<td>
<p>Maszyna wirtualna została zamknięta z poziomu systemu operacyjnego gościa lub przy użyciu interfejsów API usługi PowerOff.</p>
<p>Sprzęt jest nadal przydzielany do maszyny Wirtualnej i pozostaje na hoście. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>&#42;z rozliczeniami</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofanie przydziału</b></p>
</td>
<td>
<p>Stan przejściowy. Po zakończeniu maszyna wirtualna będzie kazywać się jako **cofnięta.**</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nie naliczane&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Cofnięto przydział</b></p>
</td>
<td>
<p>Maszyna wirtualna została zatrzymana pomyślnie i usunięta z hosta. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Niezaliczone rachunki</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Niektóre zasoby platformy Azure, takie jak dyski i sieci, ponoszą opłaty. Licencje na oprogramowanie w instancji nie są naliczane opłaty.

## <a name="provisioning-states"></a>Stany inicjowania obsługi administracyjnej

Stan inicjowania obsługi administracyjnej jest stanem zainicjowanej przez użytkownika operacji płaszczyzny sterowania na maszynie wirtualnej. Te stany są oddzielone od stanu zasilania maszyny Wirtualnej.

- **Tworzenie** — tworzenie maszyny Wirtualnej.

- **Aktualizacja** — aktualizuje model dla istniejącej maszyny Wirtualnej. Niektóre zmiany nie-model do maszyny Wirtualnej, takich jak Start/Restart również podlegają aktualizacji.

- **Usuń** — usunięcie maszyny Wirtualnej.

- **Deallocate** — jest, gdy maszyna wirtualna jest zatrzymana i usunięta z hosta. Rozdzielanie maszyny Wirtualnej jest uważane za aktualizację, więc będzie wyświetlać stany inicjowania obsługi administracyjnej związane z aktualizacją.



Oto przejściowe stany operacji po zaakceptowaniu przez platformę akcji zainicjowanej przez użytkownika:

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
<p><b>Stany inicjowania obsługi administracyjnej systemu operacyjnego</b></p>
</td>
<td width="366">
<p>Jeśli maszyna wirtualna jest tworzona z obrazem systemu operacyjnego, a nie z obrazem wyspecjalizowanym, można zaobserwować następujące podstany:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; Maszyna wirtualna jest uruchomiona i instalacja systemu operacyjnego gościa jest w toku. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningUzupełnianie</b> &ndash; stanu krótkotrwałego. Maszyna wirtualna szybko przechodzi do **sukcesu,** chyba że należy zainstalować wszystkie rozszerzenia. Instalacja rozszerzeń może zająć trochę czasu. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Uwaga:</b>Inicjowanie obsługi administracyjnej systemu operacyjnego może przejść do **nie powiodło się,** jeśli występuje błąd systemu operacyjnego lub system operacyjny nie zostanie zainstalowany na czas. Klienci będą naliczane opłaty za wdrożoną maszynę wirtualną w infrastrukturze.</p>
</td>
</tr>
</table>


Po zakończeniu operacji maszyna wirtualna przejdzie do jednego z następujących stanów:

- **Powiodło się** — akcje inicjowane przez użytkownika zostały zakończone.

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

 

- **Nie powiodło się** — reprezentuje operację nie powiodło się. Więcej informacji i możliwych rozwiązań można znaleźć w kodach błędów.

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

Interfejs API widoku wystąpienia zawiera informacje o stanie uruchomionym maszyny Wirtualnej. Aby uzyskać więcej informacji, zobacz dokumentację interfejsu API [widoku wystąpienia — maszyny wirtualne.](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview)

Eksplorator zasobów platformy Azure udostępnia prosty interfejs użytkownika do wyświetlania stanu uruchomionej maszyny Wirtualnej: [Eksplorator zasobów](https://resources.azure.com/).

Stany inicjowania obsługi administracyjnej są widoczne we właściwościach maszyny Wirtualnej i widoku wystąpienia. Stany zasilania są dostępne w widoku wystąpienia maszyny Wirtualnej. 

