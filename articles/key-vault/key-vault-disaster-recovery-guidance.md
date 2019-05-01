---
title: Co należy zrobić w przypadku platformy Azure usługa przerw w działaniu, który wpływa na usługę Azure Key Vault — usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku przerwania działania usługi platformy Azure, która wpływa na usługę Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 9346f3f9bd9395ac863af87d05724a76ae83fb2f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702330"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Usługa Azure Key Vault dostępność i nadmiarowość

Usługa Azure Key Vault ma wiele warstw nadmiarowości, aby upewnić się, że kluczy i wpisów tajnych pozostaną dostępne dla aplikacji nawet w przypadku awarii poszczególnych składników usługi.

Zawartość magazynu kluczy są replikowane w regionie i co najmniej 150 mil w regionie pomocniczym, natychmiast, ale w obrębie tej samej lokalizacji geograficznej. Zapewnia to wysoką trwałością kluczy i wpisów tajnych. Zobacz [sparowanych regionów platformy Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) dokumentu, aby uzyskać szczegółowe informacje na temat par regionów określonych.

W przypadku awarii pojedynczych składników w usłudze magazynu kluczy, alternatywne składników w obrębie regionu krok ma obsługiwać żądania upewnij się, że jest bez spadku funkcji. Nie trzeba podejmować żadnych działań do tego wyzwalacza. On odbywa się automatycznie i będzie przezroczysty dla użytkownika.

W rzadkich niedostępności cały region platformy Azure, są automatycznie kierowane żądania, wchodzące w usłudze Azure Key Vault w danym regionie (*przełączone w tryb failover*) do regionu pomocniczego. Gdy region podstawowy będzie znowu dostępna, są kierowane żądania Wstecz (*powrót po awarii*) do regionu podstawowego. Ponownie nie trzeba podejmować żadnych działań, ponieważ jest to wykonywane automatycznie.

Istnieje kilka zastrzeżenia, których trzeba pamiętać:

* Zdarzenia na pracę awaryjną region może upłynąć kilka minut, zanim usługa do trybu failover. Żądania, które zostały wprowadzone w tym czasie może zakończyć się niepowodzeniem do chwili zakończenia pracy w trybie failover.
* Po zakończeniu przejścia w tryb failover, Twój magazyn kluczy jest w trybie tylko do odczytu. Żądania, które są obsługiwane w tym trybie są:
  * Lista magazynów kluczy
  * Pobierz właściwości magazynów kluczy
  * Utwórz listę kluczy tajnych
  * Pobierz wpisy tajne
  * Wyświetl listę kluczy
  * Pobierz klucze (właściwości)
  * Szyfrowanie
  * Odszyfrowywanie
  * OPAKOWYWANIE
  * Dekodowanie
  * Weryfikuj
  * Podpisywanie
  * Backup
* Po przejściu w tryb failover jest powrót po awarii, wszystkie żądania typy (włącznie z odczytu *i* liczbę żądań zapisu) są dostępne.

