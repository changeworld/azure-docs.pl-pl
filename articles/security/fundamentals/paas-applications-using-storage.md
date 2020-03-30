---
title: Zabezpieczanie aplikacji PaaS przy użyciu usługi Azure Storage | Dokumenty firmy Microsoft
description: Dowiedz się więcej o najlepszych rozwiązaniach dotyczących zabezpieczeń usługi Azure Storage dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999166"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Najważniejsze wskazówki dotyczące zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS przy użyciu usługi Azure Storage
W tym artykule omówimy kolekcję najlepszych rozwiązań dotyczących zabezpieczeń usługi Azure Storage dotyczących zabezpieczania aplikacji sieci web i aplikacji mobilnych platformy jako usługi (PaaS). Te najlepsze rozwiązania pochodzą z naszego środowiska z platformą Azure i doświadczeń klientów takich jak Ty.

Platforma Azure umożliwia wdrażanie i używanie magazynu w sposób nie jest łatwo osiągalny lokalnie. Dzięki usłudze Azure Storage możesz osiągnąć wysoki poziom skalowalności i dostępności przy stosunkowo niewielkim wysiłku. Usługa Azure Storage jest nie tylko podstawą dla maszyn wirtualnych platformy Windows i Linux Azure, ale może również obsługiwać duże aplikacje rozproszone.

Usługa Azure Storage udostępnia następujące cztery usługi: magazyn obiektów Blob, magazyn tabel, magazyn kolejek i magazyn plików. Aby dowiedzieć się więcej, zobacz [Wprowadzenie do usługi Microsoft Azure Storage](/azure/storage/common/storage-introduction).

[Przewodnik zabezpieczeń usługi Azure Storage](/azure/storage/common/storage-security-guide) jest doskonałym źródłem szczegółowych informacji na temat usługi Azure Storage i zabezpieczeń. Ten artykuł najlepszych rozwiązań dotyczy na wysokim poziomie niektórych pojęć znalezionych w przewodniku zabezpieczeń i łączy do przewodnika zabezpieczeń, a także innych źródeł, aby uzyskać więcej informacji.

W tym artykule przedstawiono następujące najważniejsze wskazówki:

- Sygnatury dostępu współdzielonego (SAS)
- Kontrola dostępu oparta na rolach (RBAC)
- Szyfrowanie po stronie klienta dla danych o wysokiej wartości
- Szyfrowanie usługi Storage


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Używanie podpisu dostępu współdzielonego zamiast klucza konta magazynu
Kontrola dostępu ma kluczowe znaczenie. Aby ułatwić kontrolowanie dostępu do usługi Azure Storage, platforma Azure generuje dwa 512-bitowe klucze kont magazynu (SAK) podczas tworzenia konta magazynu. Poziom nadmiarowości kluczy umożliwia uniknięcie przerw w serwisie podczas rutynowej rotacji kluczy. 

Klucze dostępu do magazynu są wpisami tajnymi o wysokim priorytecie i powinny być dostępne tylko dla osób odpowiedzialnych za kontrolę dostępu do magazynu. Jeśli niewłaściwa osoba uzyska dostęp do tych kluczy, będzie miała pełną kontrolę nad magazynem i może zastąpić, usunąć lub dodać pliki do magazynu. Obejmuje to złośliwe oprogramowanie i inne typy zawartości, które mogą potencjalnie naruszyć problem organizacji lub klientów.

Nadal potrzebujesz sposobu, aby zapewnić dostęp do obiektów w magazynie. Aby zapewnić bardziej szczegółowy dostęp, można skorzystać z podpisu dostępu współdzielonego (SAS). Sygnatury dostępu Współdzielonego umożliwia udostępnianie określonych obiektów w magazynie dla wstępnie zdefiniowanego przedziału czasu i z określonymi uprawnieniami. Podpis dostępu współdzielonego umożliwia zdefiniowanie:

- Interwał, w którym sygnatury dostępu Współdzielonego jest prawidłowy, w tym czas rozpoczęcia i czasu wygaśnięcia.
- Uprawnienia przyznane przez sygnaturę dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego w obiekcie blob może udzielić użytkownikowi uprawnień do odczytu i zapisu do tego obiektu blob, ale nie usunąć uprawnienia.
- Opcjonalny adres IP lub zakres adresów IP, z których usługa Azure Storage akceptuje sygnaturę dostępu Współdzielonego. Na przykład można określić zakres adresów IP należących do organizacji. Zapewnia to inny środek bezpieczeństwa dla sygnatury dostępu Współdzielonego.
- Protokół, za pomocą którego usługa Azure Storage akceptuje sygnatury dostępu Współdzielonego. Tego opcjonalnego parametru można użyć, aby ograniczyć dostęp do klientów korzystających z protokołu HTTPS.

Sas umożliwia udostępnianie zawartości w taki sposób, w jaki chcesz ją udostępnić bez rozdawania kluczy konta magazynu. Zawsze przy użyciu sygnatury dostępu Współdzielonego w aplikacji jest bezpieczny sposób udostępniania zasobów magazynu bez naruszania kluczy konta magazynu.

Aby dowiedzieć się więcej o podpisie dostępu współdzielonego, zobacz [Korzystanie z podpisów dostępu współdzielonego](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach
Innym sposobem zarządzania dostępem jest użycie [kontroli dostępu opartej na rolach](/azure/role-based-access-control/overview) (RBAC). Z RBAC, skupić się na dając pracownikom dokładne uprawnienia, których potrzebują, w oparciu o potrzebę znać i najmniej uprzywilejowanych zasad bezpieczeństwa. Zbyt wiele uprawnień może udostępnić konto atakującym. Zbyt mało uprawnień oznacza, że pracownicy nie mogą wykonywać swojej pracy wydajnie. RBAC pomaga rozwiązać ten problem, oferując zarządzanie dostępem szczegółowe dla platformy Azure. Jest to konieczne dla organizacji, które chcą wymusić zasady zabezpieczeń dostępu do danych.

Można użyć wbudowanych ról RBAC na platformie Azure, aby przypisać uprawnienia do użytkowników. Na przykład użyj współautora kont magazynu dla operatorów chmury, którzy muszą zarządzać kontami magazynu i roli współautora klasycznego konta magazynu, aby zarządzać klasycznymi kontami magazynu. Dla operatorów chmury, którzy muszą zarządzać maszynami wirtualnymi, ale nie sieci wirtualnej lub konta magazynu, do którego są połączone, można dodać je do roli współautora maszyny wirtualnej.

Organizacje, które nie wymuszają kontroli dostępu do danych przy użyciu funkcji, takich jak RBAC może dać więcej uprawnień niż jest to konieczne dla swoich użytkowników. Może to prowadzić do naruszenia bezpieczeństwa danych, umożliwiając niektórym użytkownikom dostęp do danych, których nie powinni mieć w pierwszej kolejności.

Aby dowiedzieć się więcej o RBAC zobacz:

- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](/azure/role-based-access-control/role-assignments-portal)
- [Wbudowane role dla zasobów platformy Azure](/azure/role-based-access-control/built-in-roles)
- [Przewodnik po zabezpieczeniach usługi Azure Storage](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Używanie szyfrowania po stronie klienta dla danych o wysokiej wartości
Szyfrowanie po stronie klienta umożliwia programowe szyfrowanie danych podczas przesyłania przed przekazaniem do usługi Azure Storage i programowo odszyfrowywanie danych podczas ich pobierania. Zapewnia to szyfrowanie przesyłanych danych, ale zapewnia również szyfrowanie danych w spoczynku. Szyfrowanie po stronie klienta jest najbezpieczniejszą metodą szyfrowania danych, ale wymaga wprowadzenia zmian programowych w aplikacji i wprowadzenia procesów zarządzania kluczami.

Szyfrowanie po stronie klienta umożliwia również wyłączną kontrolę nad kluczami szyfrowania. Możesz generować własne klucze szyfrowania i zarządzać nimi. Używa techniki koperty, w której biblioteka klienta magazynu azure generuje klucz szyfrowania zawartości (CEK), który jest następnie zawijany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być asymetryczną parą kluczy lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w [usłudze Azure Key Vault.](/azure/key-vault/key-vault-overview)

Szyfrowanie po stronie klienta jest wbudowane w biblioteki java i klienta magazynu .NET. Informacje na temat szyfrowania danych w aplikacjach klienckich oraz zarządzania własnymi kluczami szyfrowania i zarządzania nimi można znaleźć w obszarze [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage.](/azure/storage/common/storage-client-side-encryption)

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Włącz szyfrowanie usługi magazynu dla danych w stanie spoczynku
Gdy włączone jest [szyfrowanie usługi magazynowania](/azure/storage/common/storage-service-encryption) plików, dane są szyfrowane automatycznie przy użyciu szyfrowania AES-256. Firma Microsoft obsługuje wszystkie szyfrowanie, odszyfrowywanie i zarządzanie kluczami. Ta funkcja jest dostępna dla typów nadmiarowości LRS i GRS.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono kolekcję najlepszych rozwiązań dotyczących zabezpieczeń usługi Azure Storage dotyczących zabezpieczania aplikacji sieci Web i aplikacji mobilnych PaaS. Aby dowiedzieć się więcej o zabezpieczaniu wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](paas-deployments.md)
- [Zabezpieczanie aplikacji internetowych i mobilnych PaaS przy użyciu usług Azure App Services](paas-applications-using-app-services.md)
- [Zabezpieczanie baz danych PaaS na platformie Azure](paas-applications-using-sql.md)
