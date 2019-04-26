---
title: Środowiska zabezpieczeń usługi Azure Key Vault | Dokumentacja firmy Microsoft
ms.service: key-vault
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 07/03/2017
ms.openlocfilehash: 86438ab7ebecc53aa716e72662965276b19f1b96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60461301"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Środowiska zabezpieczeń usługi Azure Key Vault i granicach geograficznych

Usługa Azure Key Vault to usługa dla wielu dzierżawców i korzysta z puli sprzętowych modułów zabezpieczeń (HSM) w każdej lokalizacji platformy Azure. 

Wszystkie moduły HSM w lokalizacjach platformy Azure, w tym samym regionie geograficznym udostępnianie tej samej granicy kryptograficznych (środowiska zabezpieczeń firmy Thales). Na przykład wschodnie stany USA i zachodnie stany USA udostępnianie tego samego środowiska zabezpieczeń security world, ponieważ należą one do lokalizacji geograficznej w Stanach Zjednoczonych. Podobnie wszystkich lokalizacji platformy Azure w Japonii udostępnianie tego samego środowiska zabezpieczeń security world oraz wszystkich lokalizacji platformy Azure w Australii, Indie i tak dalej. 

## <a name="backup-and-restore-behavior"></a>Zachowanie i przywracania kopii zapasowych

Można przywrócić kopii zapasowej klucza z magazynu kluczy w jednej lokalizacji platformy Azure do magazynu kluczy w innej lokalizacji platformy Azure, tak długo, jak oba te warunki są spełnione:

- Zarówno lokalizacje platformy Azure należą do tej samej lokalizacji geograficznej
- Zarówno magazynów kluczy należą do tej samej subskrypcji platformy Azure

Na przykład kopię zapasową analizowaniem danej subskrypcji klucz w magazynie kluczy w Indie Zachodnie, można przywrócić tylko do innego magazynu kluczy w tej samej subskrypcji i lokalizacji geograficznej. Indie Zachodnie, Indie środkowe i Indie Południowe

## <a name="regions-and-products"></a>Regiony i produkty

- [Regiony platformy Azure](https://azure.microsoft.com/regions/)
- [Produkty firmy Microsoft uporządkowane według regionów](https://azure.microsoft.com/regions/services/)

Regiony są mapowane do środowiska zabezpieczeń, wyświetlana jako głównych pozycji w tabelach:

W produktach artykule region, na przykład **Południową** karta zawiera wschodnie stany USA, ŚRODKOWE stany USA, zachodnie stany USA wszystkich mapowań do regionu Południową. 

>[!NOTE]
>Wyjątek jest dod — wschodnie stany USA i dod — ŚRODKOWE stany USA własne środowiska zabezpieczeń. 

Podobnie na **Europa** kartę, EUROPA Północna i EUROPA ZACHODNIA, są mapowane na region Europa. To samo dotyczy również na **Azja i Pacyfik** kartę.



