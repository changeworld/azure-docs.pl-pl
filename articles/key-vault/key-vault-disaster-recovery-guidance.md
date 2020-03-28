---
title: Co zrobić w przypadku zakłóceń w działaniu usługi platformy Azure, które mają wpływ na usługę Azure Key Vault — Usługa Azure Key Vault | Dokumenty firmy Microsoft
description: Dowiedz się, co należy zrobić w przypadku zakłóceń w działaniu usługi platformy Azure, które mają wpływ na usługę Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 530ae2b795b4d94802e9f0d3420f7b3af86936ad
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78184642"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Dostępność i nadmiarowość usługi Azure Key Vault

Usługa Azure Key Vault oferuje wiele warstw nadmiarowości, aby upewnić się, że klucze i wpisy tajne pozostają dostępne dla aplikacji, nawet jeśli poszczególne składniki usługi nie powiodą się.

Zawartość magazynu kluczy są replikowane w regionie i do regionu pomocniczego co najmniej 150 mil, ale w tej samej lokalizacji geograficznej. Pozwala to zachować wysoką trwałość kluczy i tajemnic. Zobacz dokument [regionów sparowanych platformy Azure,](../best-practices-availability-paired-regions.md) aby uzyskać szczegółowe informacje na temat par określonych regionów.

Jeśli poszczególne składniki w usłudze magazynu kluczy nie powiedzie się, alternatywne składniki w regionie krok w celu obsługi żądania, aby upewnić się, że nie ma degradacji funkcjonalności. Nie trzeba podejmować żadnych działań, aby wyzwolić to. Dzieje się to automatycznie i będzie dla Ciebie przezroczyste.

W rzadkich przypadkach, gdy cały region platformy Azure jest niedostępny, żądania, które można zrobić z usługi Azure Key Vault w tym regionie są automatycznie kierowane *(nie powiodło się)* do regionu pomocniczego. Gdy region podstawowy jest ponownie dostępny, żądania są kierowane z powrotem *(po awarii z powrotem)* do regionu podstawowego. Ponownie, nie trzeba podejmować żadnych działań, ponieważ dzieje się to automatycznie.

Dzięki tej konstrukcji o wysokiej dostępności usługa Azure Key Vault nie wymaga żadnych przestojów w działaniach konserwacyjnych.

Istnieje kilka zastrzeżeń, o których należy pamiętać:

* W przypadku prze sprawnienia stanu failover regionu może upłynąć kilka minut, aby usługa została przejęta awaryjnie. Żądania, które są dokonywane w tym czasie może zakończyć się niepowodzeniem, dopóki nie zostanie zakończona pracy awaryjnej.
* Po zakończeniu pracy awaryjnej magazyn kluczy jest w trybie tylko do odczytu. Żądania, które są obsługiwane w tym trybie są:
  * Wyświetlanie listy magazynów kluczy
  * Uzyskaj właściwości magazynów kluczy
  * Lista wpisów tajnych
  * Zdobądź sekrety
  * Klawisze listy
  * Pobierz (właściwości) kluczy
  * Szyfrowanie
  * Odszyfrowywanie
  * Zawijanie
  * Unwrap
  * Weryfikuj
  * Znak
  * Tworzenie kopii zapasowych
* Po awarii po awarii, wszystkie typy żądań (w tym *odczytu i* zapisu żądań) są dostępne.

