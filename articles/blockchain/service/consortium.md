---
title: Konsorcjum usługi Azure Blockchain Service
description: Omówienie sposobu implementowania sieci blockchain przez firmę Azure Blockchain.
ms.date: 11/21/2019
ms.topic: conceptual
ms.reviewer: zeyadr
ms.openlocfilehash: 7b8885ba08d35db20d1eb7e75141cb173913b386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247620"
---
# <a name="azure-blockchain-service-consortium"></a>Konsorcjum usługi Azure Blockchain Service

Korzystając z usługi Azure Blockchain Service, możesz tworzyć prywatne sieci blockchain konsorcjum, w których każda sieć blockchain może być ograniczona do określonych uczestników sieci. Tylko uczestnicy sieci blockchain prywatnego konsorcjum mogą przeglądać łańcuch bloków i wchodzić w interakcje z nim. Sieci konsorcjum w usłudze Azure Blockchain może zawierać dwa typy ról uczestników członkowskich:

* **Administrator** — uprzywilejowani uczestnicy, którzy mogą podejmować działania związane z zarządzaniem konsorcjum i mogą uczestniczyć w transakcjach blockchain.

* **Użytkownik** — uczestnicy, którzy nie mogą podjąć żadnych działań związanych z zarządzaniem konsorcjum, ale mogą uczestniczyć w transakcjach blockchain.

Sieci konsorcjum mogą być kombinacją ról uczestników i mogą mieć dowolną liczbę każdego typu roli. Musi istnieć co najmniej jeden administrator.

Na poniższym diagramie przedstawiono sieć konsorcjum z wieloma uczestnikami:

![Diagram sieci konsorcjum prywatnego](./media/consortium/network-diagram.png)

Dzięki zarządzaniu konsorcjum w usłudze Azure Blockchain Service możesz zarządzać uczestnikami w sieci konsorcjów. Zarządzanie konsorcjum opiera się na modelu konsensusu sieci. W bieżącej wersji zapoznawczej usługa Azure Blockchain Service udostępnia scentralizowany model konsensusu do zarządzania konsorcjum. Każdy uprzywilejowany uczestnik z rolą administrowania może podjąć działania zarządzania konsorcjum, takie jak dodawanie lub usuwanie uczestników z sieci.

## <a name="roles"></a>Role

Uczestnikami konsorcjum mogą być osoby fizyczne lub organizacje i można przypisać rolę użytkownika lub rolę administratora. W poniższej tabeli wymieniono różnice wysokiego poziomu między tymi dwiema rolami:

| Akcja | Rola użytkownika | Rola Administrator
|--------|:----:|:------------:|
| Tworzenie nowego członka | Tak | Tak |
| Zapraszanie nowych członków | Nie | Tak |
| Ustawianie lub zmienianie roli uczestnika członka | Nie | Tak |
| Zmienianie nazwy wyświetlanej elementu członkowskiego | Tylko dla własnego członka | Tylko dla własnego członka |
| Usuwanie członków | Tylko dla własnego członka | Tak |
| Weź udział w transakcjach blockchain | Tak | Tak |

### <a name="user-role"></a>Rola użytkownika

Użytkownicy są uczestnikami konsorcjum bez możliwości administratora. Nie mogą uczestniczyć w zarządzaniu członkami związanymi z konsorcjum. Użytkownicy mogą zmienić nazwę wyświetlaną swojego elementu członkowskiego i usunąć się z konsorcjum.

### <a name="administrator"></a>Administrator

Administrator może zarządzać członkami w ramach konsorcjum. Administrator może zapraszać członków, usuwać członków lub aktualizować role członków w ramach konsorcjum.
Zawsze musi istnieć co najmniej jeden administrator w ramach konsorcjum. Ostatni administrator musi określić innego uczestnika jako rolę administratora przed opuszczeniem konsorcjum.

## <a name="managing-members"></a>Zarządzanie członkami

Tylko administratorzy mogą zapraszać innych uczestników do konsorcjum. Administratorzy zapraszają uczestników przy użyciu identyfikatora subskrypcji platformy Azure.

Po zaproszeniu uczestnicy mogą dołączyć do konsorcjum blockchain, wdrażając nowego członka usługi Azure Blockchain Service. Aby wyświetlić zaproszone konsorcjum i dołączyć do niego, należy określić ten sam identyfikator subskrypcji platformy Azure, który jest używany w zaproszeniu przez administratora sieci.

Administratorzy mogą usunąć dowolnego uczestnika z konsorcjum, w tym innych administratorów. Członkowie mogą usuwać się tylko z konsorcjum.

## <a name="consortium-management-smart-contract"></a>Inteligentna umowa na zarządzanie konsorcjum

Zarządzanie konsorcjami w usłudze Azure Blockchain service odbywa się za pośrednictwem inteligentnych kontraktów zarządzania konsorcjami. Kontrakty inteligentne są automatycznie wdrażane w węzłach podczas wdrażania nowego członka łańcucha bloków.

Adres inteligentnego kontraktu zarządzania zarządzania głównym konsorcjum można wyświetlić w witrynie Azure portal. **Adres RootContract** znajduje się w sekcji przegląd członka łańcucha bloków.

![Adres RootContract](./media/consortium/rootcontract-address.png)

Inteligentnego kontraktu zarządzania konsorcjum można wchodzić w interakcje przy użyciu [modułu PowerShell](manage-consortium-powershell.md)zarządzania konsorcjum, portalu Azure lub bezpośrednio za pośrednictwem inteligentnego kontraktu przy użyciu konta Ethereum wygenerowanej usługi Azure Blockchain.

## <a name="ethereum-account"></a>Konto Ethereum

Podczas tworzenia członka tworzony jest klucz konta Ethereum. Usługa Azure Blockchain service używa klucza do tworzenia transakcji związanych z zarządzaniem konsorcjum. Klucz konta Ethereum jest automatycznie zarządzany przez usługę Azure Blockchain Service.

Konto członkowskie można wyświetlić w witrynie Azure portal. Konto członkowskie znajduje się w sekcji przeglądu użytkownika łańcucha bloków.

![Konto członkowskie](./media/consortium/member-account.png)

Możesz zresetować swoje konto Ethereum, klikając na swoje konto członkowskie i wprowadzając nowe hasło. Zarówno adres konta Ethereum, jak i hasło zostaną zresetowane.  

## <a name="next-steps"></a>Następne kroki

Akcje zarządzania konsorcjum są dostępne za pośrednictwem programu PowerShell. Aby uzyskać więcej informacji, zobacz [Zarządzanie członkami konsorcjum w usłudze Azure Blockchain service przy użyciu programu PowerShell.](manage-consortium-powershell.md)
