---
title: Zabezpieczanie aplikacji PaaS przy użyciu usługi Azure Storage | Dokumentacja firmy Microsoft
description: " Więcej informacji na temat zabezpieczeń usługi Azure Storage najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 9d4251e61b60d8da6ce5072ba66aeaedb60cb33a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych przy użyciu usługi Azure Storage
W tym artykule omówiono kolekcję usługi Azure Storage zabezpieczeń najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z platformy Azure i doświadczenia klientów, takie jak samodzielnie.

[Przewodnik zabezpieczeń usługi Azure Storage](../storage/common/storage-security-guide.md) jest doskonałym źródłem szczegółowe informacje dotyczące usługi Azure Storage i zabezpieczeń.  W tym artykule opisano na wysokim poziomie niektóre z pojęć w przewodniku po zabezpieczeniach i linki do innych źródeł, aby uzyskać więcej informacji, a także w przewodniku po zabezpieczeniach.

## <a name="azure-storage"></a>Azure Storage
Azure umożliwia wdrażanie i sposoby używania magazynu lokalnego łatwo nieosiągalne. Z usługą Azure storage można osiągnąć wysoką skalowalność i dostępność o stosunkowo dużego wysiłku. Nie tylko stanowi podstawę magazynu Azure dla systemu Windows i maszyn wirtualnych systemu Linux platformy Azure, może również obsługiwać dużych aplikacji rozproszonej.

Magazyn Azure obejmuje cztery następujące usługi: Magazyn obiektów Blob, Magazyn tabel, Magazyn kolejek i Magazyn plików. Aby dowiedzieć się więcej, zobacz [wprowadzenie do usługi Microsoft Azure Storage](../storage/storage-introduction.md).

## <a name="best-practices"></a>Najlepsze praktyki
W tym artykule opisano następujące najlepsze rozwiązania:

- Ochrona dostępu do:
   - Sygnatury dostępu współdzielonego (SAS)
   - Dysk zarządzany
   - Kontrola dostępu oparta na rolach (RBAC)

- Szyfrowanie magazynu:
   - Szyfrowanie po stronie klienta dla danych wysokiej wartości.
   - Szyfrowanie dysków Azure maszynach wirtualnych (VM)
   - Szyfrowanie usługi Storage

## <a name="access-protection"></a>Ochrona dostępu do
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Użyj sygnatura dostępu współdzielonego zamiast klucz konta magazynu

W rozwiązaniu IaaS, zazwyczaj uruchomionych maszyn wirtualnych systemu Windows Server lub Linux pliki są chronione przed ujawnieniem oraz manipulacją zagrożeń przy użyciu mechanizmy kontroli dostępu. W systemie Windows można użyć [(ACL). listy kontroli dostępu](../virtual-network/virtual-networks-acl.md) i w systemie Linux prawdopodobnie użyje [chmod](https://en.wikipedia.org/wiki/Chmod). Zasadniczo jest dokładnie co może zrobić, jeśli zostały ochrona plików na serwerze w centrum danych dzisiaj.

PaaS różni się. Jednym ze sposobów najczęściej używane do przechowywania plików na platformie Microsoft Azure jest użycie [magazynu obiektów Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md). Różnica między magazynu obiektów Blob i innych magazyn plików jest We/Wy plików i metod ochrony, które pochodzą z We/Wy plików.

Bardzo ważne jest kontrola dostępu. Ułatwiają kontrolowanie dostępu do magazynu Azure, system spowoduje wygenerowanie dwa klucze konta magazynu 512-bitowe (SAKs) po możesz [Utwórz konto magazynu](../storage/common/storage-create-storage-account.md). Poziom nadmiarowości klucza pozwala uniknąć przerwanie usługi podczas rutynowej rotacją kluczy.

Klucze dostępu do magazynu są klucze tajne o wysokim priorytecie i tylko powinien być dostępny w odpowiedzialne magazynu kontroli dostępu. Jeśli przez nieodpowiednie osoby uzyskać dostęp do tych kluczy, ich będzie mają pełną kontrolę nad magazynu i można zastąpić, usuń lub Dodaj pliki do pamięci masowej. W tym złośliwym oprogramowaniem i innych typów zawartości, które potencjalnie może naruszyć organizacji lub dla klientów.

Nadal potrzebujesz sposobem zapewnienia dostępu do obiektów w magazynie. Zapewnienie dostępu bardziej szczegółowego można korzystać z [sygnatura dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Sygnatury dostępu Współdzielonego umożliwia udostępnianie określonych obiektów w magazynie dla wstępnie zdefiniowanego interwału czasu i z określonymi uprawnieniami. Sygnaturę dostępu współdzielonego, można zdefiniować:

- Interwał, w którym jest prawidłowa, w tym czas rozpoczęcia i czas wygaśnięcia sygnatury dostępu Współdzielonego.
- Uprawnienia przyznane przez sygnatury dostępu Współdzielonego. Na przykład sygnatury dostępu Współdzielonego w obiekcie blob może udzielić odczytu użytkownika i uprawnienia zapisu do tego obiektu blob, ale nie usunąć uprawnienia.
- Opcjonalne adres IP lub zakres adresów IP, z których usługi Azure Storage akceptuje sygnatury dostępu Współdzielonego. Na przykład może określić zakres adresów IP należących do organizacji. Zapewnia to innej miary zabezpieczeń dla sieci SAS.
- Protokół, w którym magazyn Azure akceptuje sygnatury dostępu Współdzielonego. Ten parametr opcjonalny służy do ograniczania dostępu do klientów przy użyciu protokołu HTTPS.

Sygnatury dostępu Współdzielonego pozwala na udostępnianie zawartości w taki sposób, aby udostępnić go bez podania kluczy konta magazynu. Zawsze przy użyciu sygnatury dostępu Współdzielonego w aplikacji jest bezpiecznym sposobem udostępnianie zasobów magazynu bez naruszania kluczy konta magazynu.

Aby dowiedzieć się więcej, zobacz [przy użyciu sygnatury dostępu współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Aby dowiedzieć się więcej na temat potencjalnego ryzyka i zalecenia dotyczące ich eliminowania, zobacz [najlepszych rozwiązań przy użyciu sygnatury dostępu Współdzielonego](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Użyj zarządzanego dyski dla maszyn wirtualnych

Po wybraniu [dysków zarządzanych Azure](../storage/storage-managed-disks-overview.md), Azure zarządza kontami magazynu, których używasz dla dysków maszyny Wirtualnej. To wszystko, co należy zrobić, wybierz typ dysku (Premium lub Standard) i rozmiar dysku. Usługa Azure storage wykona resztę. Nie trzeba martwić limity skalowalności, które mogą w przeciwnym razie ma wymagane do użytkownika do wielu kont magazynu.

Aby dowiedzieć się więcej, zobacz [— często zadawane pytania o zarządzanych i niezarządzanych dyski premium](../storage/storage-faq-for-disks.md).

### <a name="use-role-based-access-control"></a>Za pomocą kontroli dostępu opartej na rolach

Wcześniej wspomniano, aby przyznać ograniczony dostęp do obiektów na koncie magazynu innym klientom bez narażania klucz konta magazynu konta przy użyciu dostępu sygnatury dostępu Współdzielonego. Czasami ryzyko związane z określoną operację względem konta magazynu przeważają korzyści wynikające z sygnatury dostępu Współdzielonego. Czasami jest łatwiejsze zarządzanie dostępem w inny sposób.

Inny sposób, aby zarządzać dostępem jest użycie [kontroli dostępu](../role-based-access-control/overview.md) (RBAC). Z RBAC, skupić się na zapewniając pracownikom dokładne uprawnienia, które są im potrzebne, na podstawie musi znać i co najmniej uprawnień zabezpieczeń zasad. Za dużo uprawnienia mogą uwidaczniać konta na ataki. Za mało uprawnienia oznacza, że pracownicy nie można pobrać ich pracować wydajnie. RBAC pomaga rozwiązać ten problem, oferując precyzyjne zarządzanie dostępem dla platformy Azure. Jest to konieczne w przypadku organizacji, które mają być wymuszać zasady zabezpieczeń dla dostępu do danych.

Można wykorzystać wbudowane role RBAC na platformie Azure, aby przypisać uprawnienia do użytkowników. Należy rozważyć użycie współautora konta magazynu dla operatorów chmury, które muszą zarządzać konta magazynu i roli klasycznego współautora konta magazynu do zarządzania klasycznych kont magazynu. Dla operatorów chmury, które muszą zarządzać maszyn wirtualnych, ale nie z wirtualnych sieci lub magazynu konta do którego są podłączeni, należy rozważyć dodanie ich do roli współautora maszyny wirtualnej.

Organizacje, które nie wymusić kontrolę dostępu danych dzięki wykorzystaniu możliwości, takie jak RBAC może nadanie więcej uprawnień niż jest to niezbędne dla użytkowników. Może to prowadzić do naruszenia danych dzięki niektórym użytkownikom dostęp do danych, które nie powinny mieć w pierwszej kolejności.

Aby dowiedzieć się więcej o RBAC, zobacz:

- [Kontrola dostępu oparta na rolach na platformie Azure](../role-based-access-control/role-assignments-portal.md)
- [Wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure](../role-based-access-control/built-in-roles.md)
- [Przewodnik po zabezpieczeniach magazynu Azure](../storage/common/storage-security-guide.md) szczegółowe informacje na temat sposobu zabezpieczenia konta magazynu o RBAC

## <a name="storage-encryption"></a>Szyfrowanie w usłudze Storage
### <a name="use-client-side-encryption-for-high-value-data"></a>Użyj szyfrowania po stronie klienta danych wysokiej wartości.

Szyfrowanie po stronie klienta umożliwia programowo szyfrowania przesyłanych danych przed przekazaniem do usługi Azure Storage i programowo odszyfrowywania danych podczas pobierania go z magazynu.  Zapewnia szyfrowanie danych podczas przesyłania, ale również zapewnia szyfrowanie danych magazynowanych.  Szyfrowanie po stronie klienta jest najbezpieczniejszą metodą szyfrowania danych, ale wymaga zmiany programowy do aplikacji i wprowadzenia procesy zarządzania kluczami.

Szyfrowanie po stronie klienta umożliwia również wyłączną kontrolę kluczy szyfrowania.  Można wygenerować i zarządzanie kluczami szyfrowania.  Szyfrowanie po stronie klienta używa technikę koperty, gdzie biblioteki klienta magazynu Azure generuje zawartości klucza szyfrowania (CEK), który jest następnie opakowane (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i można parę klucza asymetrycznego lub klucza symetrycznego i może być zarządzany lokalnie lub przechowywane w [usługi Azure Key Vault](../key-vault/key-vault-whatis.md).

Szyfrowanie po stronie klienta jest wbudowana w języku Java i biblioteki klienta magazynu .NET.  Zobacz [szyfrowania po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](../storage/storage-client-side-encryption.md) informacji na temat szyfrowania danych w aplikacjach klienckich i generowania i zarządzanie kluczy szyfrowania.

### <a name="azure-disk-encryption-for-vms"></a>Szyfrowanie dysków Azure dla maszyn wirtualnych
Szyfrowanie dysków Azure jest możliwość, która pomaga szyfrowania dysków maszyny wirtualnej systemu Windows i Linux IaaS. Szyfrowanie dysków Azure korzysta z branży funkcje BitLocker standardowych systemu Windows i DM-Crypt systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi. Rozwiązanie jest zintegrowany z usługą Azure Key Vault ułatwia kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w magazynie kluczy subskrypcji. Rozwiązanie zapewnia również, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w magazynie Azure.

Zobacz [szyfrowania dysków Azure dla systemu Windows oraz maszyny wirtualne systemu Linux IaaS](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Szyfrowanie usługi Storage
Gdy [szyfrowanie usługi Magazyn](../storage/storage-service-encryption.md) magazyn plików jest włączona, dane są szyfrowane automatycznie przy użyciu szyfrowania AES 256. Firma Microsoft podchodzi do szyfrowania, odszyfrowywania i zarządzania kluczami. Ta funkcja jest dostępna dla typów nadmiarowość LRS i GRS.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule wprowadzona w kolekcji usługi Azure Storage zabezpieczeń najlepsze rozwiązania dotyczące zabezpieczania PaaS w sieci web i aplikacji dla urządzeń przenośnych. Aby dowiedzieć się więcej na temat zabezpieczania wdrożeń typu PaaS, zobacz:

- [Zabezpieczanie wdrożeń typu PaaS](security-paas-deployments.md)
- [Zabezpieczanie PaaS w sieci web i aplikacji dla urządzeń przenośnych za pomocą usługi aplikacji Azure](security-paas-applications-using-app-services.md)
- [Zabezpieczanie PaaS baz danych na platformie Azure](security-paas-applications-using-sql.md)
