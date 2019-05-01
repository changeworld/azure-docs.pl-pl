---
title: Zabezpieczenia usługi Azure Key Vault | Dokumentacja firmy Microsoft
description: Zarządzaj uprawnieniami dostępu do usługi Azure Key Vault, klucze i wpisy tajne. Obejmuje model uwierzytelniania i autoryzacji dla usługi Key Vault i sposób zabezpieczania własnego magazynu kluczy.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: barclayn
Customer intent: As a key vault administrator, I want to learn the options available to secure my vaults
ms.openlocfilehash: 5b32e4897e718e0e411caf9ba76b036f1352bde0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64715289"
---
# <a name="azure-key-vault-security"></a>Zabezpieczenia usługi Azure Key Vault

Należy chronić klucze szyfrowania i wpisy tajne, takie jak certyfikaty, parametry połączenia i haseł w chmurze, więc w przypadku korzystania z usługi Azure Key Vault. Ponieważ są przechowywane poufne i krytyczne dane biznesowe, które należy wykonać kroki, aby zmaksymalizować bezpieczeństwo Twoich magazynów i przechowywane w nich dane. W tym artykule opisano niektóre pojęcia, które należy wziąć pod uwagę podczas projektowania zabezpieczeń usługi Azure Key Vault.

## <a name="identity-and-access-management"></a>Zarządzanie tożsamościami i dostępem

Po utworzeniu magazynu kluczy w subskrypcji platformy Azure ma automatycznie skojarzone z dzierżawą usługi Azure AD subskrypcji. Każda osoba próby zarządzania lub pobieranie zawartości z magazynu musi zostać uwierzytelniony przez usługę Azure AD.

- Uwierzytelnianie ustala tożsamość elementu wywołującego.
- Autoryzacja Określa, jakie operacje może wykonywać obiekt wywołujący. Autoryzacja w usłudze Key Vault używa kombinacji [kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) i zasad dostępu w usłudze Azure Key Vault.

### <a name="access-model-overview"></a>Omówienie modelu dostępu

Dostęp do magazynów odbywa się za pośrednictwem dwóch interfejsów lub płaszczyzny. Te płaszczyzn są płaszczyzny zarządzania i płaszczyzny danych.

- *Płaszczyzny zarządzania* zarządzania usługi Key Vault, sama i jest interfejs umożliwia tworzenie i usuwanie magazynów. Można odczytać właściwości magazynu kluczy i zarządzanie zasadami dostępu.
- *Płaszczyzny danych* umożliwia pracę z danymi przechowywanymi w magazynie kluczy. Możesz dodawać, usuwanie i modyfikowanie kluczy, wpisów tajnych i certyfikatów.

Aby uzyskać dostęp do magazynu kluczy, albo płaszczyźnie, wszystkie podmioty wywołujące (użytkownicy lub aplikacje) muszą uwierzytelnianie i autoryzowanie. Obu płaszczyzn używać usługi Azure Active Directory (Azure AD) do uwierzytelniania. Na potrzeby autoryzacji płaszczyzna zarządzania używa kontroli dostępu opartej na rolach (RBAC) i płaszczyzna danych używa zasad dostępu magazynu kluczy.

Model pojedynczy mechanizm uwierzytelniania obu płaszczyzn ma kilka zalet:

- Organizacje mogą kontrolować dostęp centralnie do wszystkich magazynów kluczy w organizacji.
- Jeśli użytkownik pozostawi, natychmiast utraci dostęp do wszystkich magazynów kluczy w organizacji.
- Organizacje mogą dostosowywać uwierzytelnianie za pomocą opcji w usłudze Azure AD, takie jak włączyć uwierzytelnianie wieloskładnikowe w celu zwiększenia poziomu bezpieczeństwa

### <a name="managing-administrative-access-to-key-vault"></a>Zarządzanie dostępem administracyjnym do usługi Key Vault

Po utworzeniu magazynu kluczy w grupie zasobów, możesz zarządzać dostępem przy użyciu usługi Azure AD. Zezwolenie użytkownikom na lub grup możliwość zarządzania magazynami kluczy w grupie zasobów. Przypisując odpowiednie role RBAC, można przyznać dostęp na poziomie określonego zakresu. Aby udzielić dostępu użytkownika do zarządzania magazynami kluczy, możesz przypisać wstępnie zdefiniowanej `key vault Contributor` roli do użytkownika o określonym zakresie. Następujące poziomy zakresów można przypisać do roli RBAC:

- **Subskrypcja**: Rola RBAC, przypisana na poziomie subskrypcji ma zastosowanie do wszystkich grup zasobów i zasobów w ramach tej subskrypcji.
- **Grupa zasobów**: Rola RBAC, przypisana na poziomie grupy zasobów ma zastosowanie do wszystkich zasobów w danej grupie zasobów.
- **Określony zasób**: Rola RBAC, przypisane do określonego zasobu ma zastosowanie do tego zasobu. W tym przypadku zasób jest określonego magazynu kluczy.

Istnieje kilka wstępnie zdefiniowanych ról. Jeśli wstępnie zdefiniowaną rolę nie odpowiadają potrzebom, można zdefiniować własne roli. Aby uzyskać więcej informacji, zobacz [RBAC: Built-in roles](../role-based-access-control/built-in-roles.md) (Kontrola dostępu oparta na rolach: role wbudowane).

> [!IMPORTANT]
> Jeśli użytkownik ma `Contributor` uprawnienia do płaszczyzny zarządzania magazynu kluczy, użytkownik może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Należy ściśle kontrolować, kto ma `Contributor` roli dostęp do Twoich magazynów kluczy. Sprawdź, czy tylko upoważnione osoby mogą uzyskać dostęp i Zarządzanie magazynami kluczy, kluczy, wpisów tajnych i certyfikatów.

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>Kontrolowanie dostępu do danych usługi Key Vault

Zasady dostępu magazynu kluczy przyznają uprawnienia oddzielnie do kluczy, wpisów tajnych lub certyfikatów. Możesz udzielić użytkownikowi dostępu tylko do kluczy, a nie wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych i certyfikatów są zarządzane na poziomie magazynu.

> [!IMPORTANT]
> Zasady dostępu magazynu kluczy nie obsługują szczegółowe, na poziomie obiektu uprawnień, takich jak określony klucz, hasło lub certyfikat. Jeśli użytkownikowi udzielono uprawnień do tworzenia i usuwania kluczy, można wykonać te operacje na wszystkie klucze, w tym magazynie kluczy.

Aby ustawić zasady dostępu magazynu kluczy, użyj [witryny Azure portal](https://portal.azure.com/), [wiersza polecenia platformy Azure](../cli-install-nodejs.md), [programu Azure PowerShell](/powershell/azureps-cmdlets-docs), lub [interfejsy API REST zarządzania magazyn klucz](https://msdn.microsoft.com/library/azure/mt620024.aspx).

Możesz ograniczyć dostęp do płaszczyzny danych za pomocą [punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Można skonfigurować [zapory i reguł sieci wirtualnej](key-vault-network-security.md) dla dodatkową warstwę zabezpieczeń.

## <a name="network-access"></a>Dostęp do sieci

Można zmniejszyć narażenie Twoich magazynów przez określenie adresów IP, które mają dostęp do nich. Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault umożliwiają ograniczenie dostępu do określonej sieci wirtualnej. Punkty końcowe umożliwiają również ograniczyć dostęp do listy zakresów adresów IPv4 (internet protocol w wersji 4). Odmowa dostępu dla każdego użytkownika, na nawiązywanie połączeń z usługi key vault z spoza tych źródeł.

Po zapory reguły działają, użytkownicy mogą tylko odczytywać dane z usługi Key Vault podczas ich żądania pochodzą z dozwolonych sieci wirtualnych lub zakres adresów IPv4. Dotyczy to również do uzyskiwania dostępu do usługi Key Vault w witrynie Azure portal. Mimo że użytkownicy mogą przeglądać do magazynu kluczy w witrynie Azure portal, ich może nie móc Wyświetl listę kluczy, wpisów tajnych lub certyfikatów Jeśli swoim komputerze klienckim nie ma na liście dozwolonych. Dotyczy to również selektor klucza magazynu przez inne usługi platformy Azure. Użytkowników można wyświetlić listę magazynów kluczy, ale nie listy kluczy, jeśli reguły zapory na swoim komputerze klienckim.

Aby uzyskać więcej informacji na temat usługi Azure Key Vault sieci przeglądu adres [punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)

## <a name="monitoring"></a>Monitorowanie

Rejestrowanie usługi Key Vault zapisuje informacje dotyczące czynności wykonywanych w magazynie usługi. Dzienników usługi Key Vault:

- Wszystkie uwierzytelnione żądania interfejsu API REST, w tym żądań zakończonych niepowodzeniem
  - Operacje na kluczu samym magazynie. Operacje te obejmują tworzenie, usuwanie, ustawianie zasad dostępu i aktualizowania atrybutów magazynu kluczy, takich jak tagi.
  - Operacje na kluczach i wpisach tajnych w magazynie kluczy, w tym:
    - Tworzenie, modyfikowanie lub usuwaniem tych kluczy lub kluczy tajnych.
    - Podpisywanie, weryfikowanie, szyfrowanie, odszyfrowywanie, zawijania i Odkodowywanie klucze pobierania wpisów tajnych oraz listę kluczy i wpisów tajnych (i ich wersje).
- Nieuwierzytelnione żądania, które powodują uzyskanie odpowiedzi 401. Przykładami są żądań, które nie mają tokenu elementu nośnego, są źle sformułowane lub wygasły lub mają nieprawidłowy token.

Rejestrowanie informacji są dostępne w ciągu 10 minut, po zakończeniu operacji magazynu kluczy. To Ty możesz zarządzasz dziennikami na swoim koncie magazynu. 

- Użyj standardowych metod kontroli dostępu platformy Azure w celu zabezpieczenia dzienników, wprowadzając ograniczenia co do tego, kto może uzyskiwać do nich dostęp.
- Usuń dzienniki, których nie chcesz już przechowywać na koncie magazynu.

Zalecenie dotyczące bezpieczne zarządzanie magazynem kont w biuletynie [Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/common/storage-security-guide.md)

## <a name="next-steps"></a>Następne kroki

- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
- [RBAC: Wbudowane role](../role-based-access-control/built-in-roles.md)
- [Punkty końcowe usługi sieci wirtualnej dla usługi Azure Key Vault](key-vault-overview-vnet-service-endpoints.md)
