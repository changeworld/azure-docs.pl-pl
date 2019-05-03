---
title: Usługa Azure Blockchain konsorcjum
description: ''
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: e745a4ee4789ef46a61b5cb0bbf806c41ef631ec
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027918"
---
# <a name="azure-blockchain-service-consortium"></a>Usługa Azure Blockchain konsorcjum

Usługa Azure Blockchain, możesz utworzyć prywatne konsorcjum sieci łańcucha bloków, gdzie każda sieć łańcucha bloków może być ograniczone do określonych uczestników w sieci. Tylko uczestnicy sieć łańcucha bloków prywatnych konsorcjum można wyświetlać i korzystać z łańcucha bloków. Konsorcjum sieci w usłudze Azure Blockchain Service może zawierać dwa typy ról uczestnika elementu członkowskiego:

* **Administrator** — uprzywilejowane uczestników, którzy mogą podejmować działania zarządzania konsorcjum i można uczestniczyć w transakcjach łańcucha bloków.

* **Użytkownik** -uczestników, którzy nie może podejmować żadnych działań zarządzania konsorcjum, ale mogą uczestniczyć w transakcjach łańcucha bloków.

Konsorcjum sieci mogą być różne role uczestnika i może zawierać dowolną liczbę poszczególnych ról. Musi istnieć co najmniej jednego administratora.

Na poniższym diagramie przedstawiono sieci konsorcjum z wieloma uczestnikami:

![Diagram sieci konsorcjum prywatne](./media/consortium/network-diagram.png)

Za pomocą funkcji zarządzania konsorcjum w usłudze Azure Service łańcucha bloków możesz zarządzać uczestników sieci konsorcjum. Zarządzanie konsorcjum opiera się na modelu consensus sieci. W bieżącej wersji zapoznawczej Azure Blockchain Service udostępnia model consensus scentralizowanego zarządzania konsorcjum. Każdy uczestnik uprzywilejowanych z rolą administratora może potrwać konsorcjum akcje zarządzania, takie jak dodawanie lub usuwanie uczestników z sieci.

## <a name="roles"></a>Role

Uczestnicy konsorcjum mogą być osoby i organizacje i można przypisać roli użytkownika lub administratora. W poniższej tabeli wymieniono Ogólne różnice między dwie role:

| Akcja | Rola użytkownika | Rola administratora
|--------|:----:|:------------:|
| Tworzenie nowego elementu członkowskiego | Yes | Yes |
| Zapraszanie nowych elementów członkowskich | Nie | Yes |
| Ustawianie lub zmienianie uczestnika roli członka | Nie | Yes |
| Zmień nazwę wyświetlaną elementu członkowskiego | Tylko dla własnej składowej | Tylko dla własnej składowej |
| Usuń członków | Tylko dla własnej składowej | Yes |
| Uczestniczenie w transakcjach łańcucha bloków | Yes | Yes |

### <a name="user-role"></a>Rola użytkownika

Użytkownicy uczestniczą konsorcjum nie możliwości administratora. Nie uczestniczą w zarządzaniu elementy członkowskie powiązane z konsorcjum. Użytkownicy mogą zmienić ich nazwy wyświetlanej członka i można usunąć siebie z konsorcjum.

### <a name="administrator"></a>Administrator

Administrator może zarządzać członków w ramach konsorcjum. Administrator może zapraszać członków, Usuń członków lub aktualizowanie ról członków w ramach konsorcjum.
Zawsze musi być co najmniej jednego administratora w ramach konsorcjum. Ostatnim administratorem należy określić innego uczestnika jako rola administratora przed opuszczeniem konsorcjum.

## <a name="managing-members"></a>Zarządzanie członkami

Tylko administratorzy mogą zapraszać innych uczestników konsorcjum. Administratorzy zapraszać uczestników przy użyciu swojego identyfikatora subskrypcji platformy Azure.

Gdy zaproszenie, mogą dołączać do konsorcjum łańcucha bloków, wdrażając nowy element członkowski w usłudze Azure Service łańcucha bloków. Do wyświetlenia, a następnie dołącz zaproszonego konsorcjum, należy określić ten sam identyfikator subskrypcji platformy Azure używane w zaproszeniu przez administratora sieci.

Administratorzy mogą usunąć każdy uczestnik z konsorcjum, łącznie z innych administratorów. Członkowie mogą tylko został usunięty z konsorcjum.

## <a name="consortium-management-smart-contract"></a>Kontrakt inteligentne zarządzanie konsorcjum

Zarządzanie konsorcjum w usłudze Azure Blockchain Service odbywa się za pośrednictwem kontraktów inteligentnych zarządzania konsorcjum. Kontrakty inteligentne są automatycznie wdrażane na węzły, podczas wdrażania nowego członka łańcucha bloków.

Adres kontraktu inteligentne zarządzanie konsorcjum głównego można wyświetlić w witrynie Azure portal. **Adres RootContract** znajduje się w sekcji Przegląd Członkowskie łańcucha bloków.

![Adres RootContract](./media/consortium/rootcontract-address.png)

Możesz wchodzić w interakcje z umową inteligentne zarządzanie konsorcjum za pomocą zarządzania konsorcjum [modułu programu PowerShell](manage-consortium-powershell.md), Azure portal, lub bezpośrednio za pomocą inteligentnego kontraktu, za pomocą usługi Azure Blockchain generowane Ethereum konto.

## <a name="ethereum-account"></a>Konto Ethereum

Po utworzeniu elementu członkowskiego jest tworzony klucz konta Ethereum. Usługa Azure Blockchain używa klucza do utworzenia transakcje związane z zarządzaniem konsorcjum. Klucz konta Ethereum jest zarządzane przez usługę Azure Blockchain Service automatycznie.

Konto członka, można wyświetlać w witrynie Azure portal. Konto członka, znajduje się w sekcji Przegląd Członkowskie łańcucha bloków.

![Konto członka](./media/consortium/member-account.png)

Możesz zresetować Twoje konto Ethereum, klikając na Twoim koncie elementu członkowskiego i wprowadzenie nowego hasła. Adres Ethereum konta i hasło zostaną zresetowane.  

## <a name="next-steps"></a>Kolejne kroki

[Jak zarządzać elementy członkowskie w usłudze Azure Service łańcucha bloków przy użyciu programu PowerShell](manage-consortium-powershell.md)
