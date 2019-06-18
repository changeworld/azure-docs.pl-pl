---
title: Zabezpieczanie aplikacji PaaS przy użyciu usługi Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o usłudze Azure Storage security najlepsze rozwiązania dotyczące zabezpieczania usługi PaaS w sieci web i aplikacji mobilnych.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: 3ad97c7adb5901c1da1d174d12d5d6a91831cc74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60445421"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i mobilnych przy użyciu usługi Azure Storage
W tym artykule omawiane jest kolekcją usługi Azure Storage najlepsze rozwiązania dotyczące zabezpieczania platformy jako usługi (PaaS) w sieci web i aplikacji mobilnych. Następujące najlepsze rozwiązania są uzyskiwane z naszych doświadczeń z platformą Azure i procesy, przez klientów, takich jak samodzielnie.

Dzięki systemowi Azure można wdrażać i sposoby używania magazynu nie jest to prosty sposób osiągalne w środowisku lokalnym. Z usługą Azure storage możesz połączyć wysoki poziom skalowalności i dostępności przy użyciu względnie niewielkim nakładzie pracy. Nie tylko jest podstawą usługi Azure Storage dla Windows i Linux Azure Virtual Machines, może również obsługiwać duże aplikacje rozproszone.

Usługa Azure Storage udostępnia cztery następujące usługi: Blob storage, Table storage, usługi Queue storage i File storage. Aby dowiedzieć się więcej, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../storage/storage-introduction.md).

[Przewodnik po zabezpieczeniach usługi Azure Storage](../storage/common/storage-security-guide.md) to doskonałe źródło pomocy szczegółowe informacje dotyczące usługi Azure Storage i zabezpieczeń. Na wysokim poziomie niektóre kwestie poruszane w Przewodnik po zabezpieczeniach i łącza do innych źródeł, aby uzyskać więcej informacji, a także Przewodnik po zabezpieczeniach dotyczy to najlepsze rozwiązania dotyczące programu.

W tym artykule opisano następujące najlepsze rozwiązania:

- Sygnatury dostępu współdzielonego (SAS)
- Kontrola dostępu oparta na rolach (RBAC)
- Szyfrowanie po stronie klienta dla danych o wysokiej wartości
- Szyfrowanie usługi Storage


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Użyj sygnatury dostępu współdzielonego zamiast klucza konta magazynu
Kontrola dostępu ma krytyczne znaczenie. Aby ułatwić kontroli dostępu do usługi Azure Storage, Azure generuje dwa klucze konta magazynu 512-bitowe (SAKs) po utworzeniu konta magazynu. Poziom nadmiarowości klucza pozwala uniknąć przerw w świadczeniu usługi podczas procedury wymiany kluczy. 

Klucze dostępu do magazynu wpisy tajne o wysokim priorytecie i powinny być dostępne tylko te, które są odpowiedzialne za kontrola dostępu do magazynu. Jeśli niepowołane osoby uzyskują dostęp do tych kluczy, one będzie mają pełną kontrolę nad magazynu i można zastąpić, usuń lub Dodaj pliki do magazynu. Obejmuje to złośliwe oprogramowanie i inne typy zawartości, która potencjalnie może naruszyć swoją organizację lub klientów.

Nadal potrzebujesz sposobu na podanie dostęp do obiektów w magazynie. Aby zapewnić bardziej szczegółową dostęp możesz korzystać z zalet sygnatury dostępu współdzielonego (SAS). Sygnatura dostępu Współdzielonego umożliwia udostępnianie konkretne obiekty w magazynie dla wstępnie zdefiniowanego przedziału czasu i z określonymi uprawnieniami. Sygnatury dostępu współdzielonego umożliwia definiowanie:

- Interwał, przez który sygnatura dostępu Współdzielonego jest prawidłowy, tym czas rozpoczęcia i czas wygaśnięcia.
- Uprawnienia przyznane przez sygnatury dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego dla obiektu blob może udzielić odczytu użytkownika i uprawnienia do zapisu do tego obiektu blob, ale nie usunąć uprawnienia.
- Opcjonalny adres IP lub zakres adresów IP, z których usługi Azure Storage akceptuje sygnatury dostępu Współdzielonego. Na przykład można określić zakres adresów IP należących do organizacji. Dzięki temu kolejną miarę zabezpieczeń usługi sygnatur dostępu współdzielonego.
- Protokół, nad którym usługi Azure Storage akceptuje sygnatury dostępu Współdzielonego. Ten parametr opcjonalny można użyć, aby ograniczyć dostęp do klientów przy użyciu protokołu HTTPS.

Sygnatury dostępu Współdzielonego umożliwia udostępnianie zawartości w sposób udostępnić go bez podania kluczy konta magazynu. Zawsze przy użyciu sygnatury dostępu Współdzielonego w aplikacji jest bezpiecznym sposobem udostępniania zasobów magazynu bez narażania kluczy konta magazynu.

Aby dowiedzieć się więcej na temat sygnatury dostępu współdzielonego, zobacz [używanie sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Korzystanie z kontroli dostępu opartej na rolach
Innym sposobem, aby zarządzać dostępem jest użycie [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md) (RBAC). Przy użyciu RBAC, skupić się na zapewniając pracownikom dokładnie uprawnienia, które są im potrzebne, oparte na znajomości i zasad zabezpieczeń w usłudze najniższych uprawnień. Zbyt wiele uprawnień może narazić konta dla osób atakujących. Zbyt mało uprawnienia oznacza, że pracownicy nie może wykonać swoją pracę wydajnie. RBAC pomaga rozwiązać ten problem, udostępniając precyzyjne zarządzanie dostępem dla platformy Azure. Jest to konieczne dla organizacji, które chcesz wymuszać zasady zabezpieczeń dostępu do danych.

Aby przypisać uprawnienia do użytkowników, można użyć wbudowane role kontroli RBAC na platformie Azure. Na przykład użyć Współautor konta magazynu dla operatorów chmury, które muszą zarządzać kontami magazynu i roli współautora klasycznej konta magazynu do zarządzania kontami klasycznego magazynu. Dla operatorów chmury wymagających zarządzania maszynami wirtualnymi, ale nie sieci wirtualnej i konta magazynu, z którym są połączone możesz dodać je do roli Współautor maszyny wirtualnej.

Organizacje, które nie wymusza kontrola dostępu do danych przy użyciu funkcji, takich jak RBAC może podając więcej uprawnień niż jest to konieczne, dla swoich użytkowników. Może to prowadzić do naruszenia zabezpieczeń danych, umożliwiając niektórym użytkownikom dostęp do danych, które nie powinny mieć w pierwszej kolejności.

Aby dowiedzieć się więcej o ROLACH, zobacz:

- [Manage access using RBAC and the Azure portal (Zarządzanie dostępem przy użyciu kontroli dostępu opartej na rolach i witryny Azure Portal)](../role-based-access-control/role-assignments-portal.md)
- [Wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md)
- [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Użyj szyfrowania po stronie klienta w przypadku danych o wysokiej wartości
Szyfrowanie po stronie klienta umożliwia programowo szyfrować przesyłane dane przed przekazaniem do usługi Azure Storage i programowo odszyfrowywania danych podczas pobierania go. To zapewnia szyfrowanie danych podczas przesyłania, ale również umożliwia szyfrowanie danych magazynowanych. Szyfrowanie po stronie klienta jest najbezpieczniejszą metodą szyfruje Twoje dane, ale wymaga zmiany programowy do aplikacji i umieścić procesów zarządzania kluczami w miejscu.

Szyfrowanie po stronie klienta umożliwia także wyłączną kontrolę nad kluczami szyfrowania. Można wygenerować i zarządzać kluczami szyfrowania. Jego wykorzystuje technikę koperty, w którym biblioteki klienta magazynu Azure generuje zawartości klucza szyfrowania (CEK), który jest następnie opakowywany (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). Klucza KEK jest identyfikowana przez identyfikator klucza i można asymetryczną parę kluczy lub klucz symetryczny i mogą być zarządzane lokalnie lub przechowywane w [usługi Azure Key Vault](../key-vault/key-vault-whatis.md).

Szyfrowanie po stronie klienta jest wbudowana w języku Java i biblioteki klienta .NET magazynu. Zobacz [szyfrowanie po stronie klienta i usługi Azure Key Vault dla usługi Microsoft Azure Storage](../storage/storage-client-side-encryption.md) informacji na temat szyfrowania danych w aplikacjach klienckich i generowania i zarządzanie kluczami szyfrowania.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Włącz szyfrowanie usługi Storage dla danych magazynowanych
Gdy [szyfrowanie usługi Storage](../storage/storage-service-encryption.md) magazyn plików jest włączone, dane są szyfrowane automatycznie przy użyciu szyfrowania AES-256. Firma Microsoft podchodzi do wszystkich szyfrowanie, odszyfrowywanie i zarządzanie kluczami. Ta funkcja jest dostępna dla typów nadmiarowości magazynu LRS i GRS.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł zawiera wprowadzenie do kolekcji usługi Azure Storage najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji mobilnych. Aby dowiedzieć się więcej na temat zabezpieczanie wdrożeń PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS w sieci web i mobilnych przy użyciu usługi Azure App Services](security-paas-applications-using-app-services.md)
- [Zabezpieczanie PaaS baz danych na platformie Azure](security-paas-applications-using-sql.md)
