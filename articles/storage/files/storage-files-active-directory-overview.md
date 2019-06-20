---
title: Omówienie uwierzytelniania usługi Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza) — usługi Azure Storage
description: Usługa pliki systemu Azure obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) za pomocą usług domenowych Azure Active Directory (Azure AD). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/18/2019
ms.author: rogarana
ms.openlocfilehash: 21087424be1a7a3edfe2dddcbec830bd74559b23
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269359"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-aad-ds-authentication-support-for-smb-access-preview"></a>Omówienie usługi Azure Files usługi Azure Active Directory domeny usługi AAD obsługę uwierzytelniania dostępu do protokołu SMB (wersja zapoznawcza)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć uwierzytelnianie usługi AAD DS dla usługi Azure Files, zobacz [Włącz Azure domeny usługi uwierzytelniania usługi Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Słownik 
Warto poznać kilka kluczowych pojęć odnoszących się do usług domenowych Azure AD authentication za pośrednictwem protokołu SMB dla usługi Azure Files:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) to usługa firmy Microsoft wieloma dzierżawami opartej na chmurze zarządzania katalogami i tożsamościami zarządzania. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochrony tożsamości w ramach jednego rozwiązania. Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Usługi domenowe Azure AD oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i Kerberos/NTLM uwierzytelniania. Te usługi są w pełni zgodne z usługą Windows Server Active Directory. Aby uzyskać więcej informacji, zobacz [usługi domenowe Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **Kontrola dostępu (RBAC) oparta na rolach platformy Azure**  
    Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Przy użyciu funkcji RBAC, można zarządzać dostępem do zasobów przez udzielenie użytkownikom najmniejsze uprawnienia wymagane do wykonywania swoich zadań. Aby uzyskać więcej informacji o ROLACH, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure?](../../role-based-access-control/overview.md)

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania, która jest używana do zweryfikowania tożsamości danego użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz [uwierzytelnianie Kerberos — omówienie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół Message Block (SMB) serwera**  
    SMB to protokół udostępniania plików sieciowych będące standardami branżowymi. Protokół SMB jest również nazywany Common Internet File System lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [protokołu SMB firmy Microsoft oraz omówienie protokołu CIFS](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Korzyści wynikające z uwierzytelniania usługi domenowe Azure AD
Uwierzytelnianie usług domenowych AD systemu Azure dla usługi Azure Files oferuje kilka korzyści w stosunku do przy użyciu uwierzytelniania klucza wspólnego:

-   **Rozszerzanie środowiska dostępu do udziału tradycyjnych na podstawie tożsamości plików do chmury dzięki usłudze Azure AD i Azure AD usług domenowych**  
    Jeśli planujesz metodą "lift and shift" poświadczeń aplikacji w chmurze, zastępując tradycyjnych serwerów plików usługi Azure Files, a następnie może być aplikacji do uwierzytelniania za pomocą usługi Azure AD, dostęp do danych plików. Platforma Azure obsługuje pliki przy użyciu poświadczeń usługi Azure AD, dostęp do usługi Azure Files za pośrednictwem protokołu SMB przy użyciu usługi AAD DS przyłączonych do domeny Windows maszyn wirtualnych. Możesz również synchronizowanie wszystkich obiektów usługi Active Directory lokalnych z usługą Azure AD, aby zachować nazwy użytkownika, hasła i inne przypisania grupy.

-   **Wymuszanie szczegółową kontrolę dostępu do udziałów plików platformy Azure**  
    Możesz nadawać uprawnienia do określonej tożsamości na poziomie udziału, katalogu lub pliku. Na przykład załóżmy, że masz kilka zespołów przy użyciu udziału plików platformy Azure w jednym projekcie współpracy. Podczas ograniczania dostępu do katalogów zawierających poufne dane finansowe do Twojego zespołu Finanse, można przyznać dostęp do zwykłych katalogi, wszystkich zespoły. 

-   **Tworzenie kopii zapasowej listy ACL wraz z danych**  
    Aby utworzyć kopię zapasową istniejących udziałów plików w środowisku lokalnym, można użyć usługi Azure Files. Usługa Azure Files zachowuje Twojej listy kontroli dostępu oraz dane udostępnienie Utwórz kopię zapasową pliku do usługi Azure Files za pośrednictwem protokołu SMB.

## <a name="how-it-works"></a>Jak to działa
Usługa Azure Files korzysta z usług domenowych Azure AD do obsługi uwierzytelniania Kerberos, przy użyciu poświadczeń usługi Azure AD z przyłączonym do domeny maszynami wirtualnymi. Zanim użyjesz usługi Azure AD za pomocą usługi Azure Files można włączyć usługi domenowe Azure AD i przyłączania do domeny na maszynach wirtualnych, z których mają dostęp do danych plików. Przyłączone do domeny maszyny Wirtualnej muszą znajdować się w tej samej sieci wirtualnej (VNET), jak usługi domenowe Azure AD. 

Tożsamość skojarzoną z aplikacji uruchomionej na maszynie Wirtualnej próbuje uzyskać dostęp do danych w usłudze Azure Files, żądanie jest wysyłane do usług domenowych Azure AD do uwierzytelniania tożsamości. Jeśli uwierzytelnianie się powiedzie, Azure AD Domain Services zwraca token protokołu Kerberos. Aplikacja wysyła żądanie, które zawiera token protokołu Kerberos i usługi Azure Files używa tego tokenu, aby autoryzować żądania. Usługa pliki systemu Azure odbiera tylko token i nie utrwala poświadczeń usługi Azure AD.

![Zrzut ekranu przedstawiający diagram uwierzytelniania usługi Azure AD przy użyciu protokołu SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Włączanie uwierzytelniania usługi Azure AD Domain dostęp przez protokół SMB
Można włączyć uwierzytelnianie usług domenowych Azure AD dla usługi Azure Files magazynu nowych i istniejących kont utworzonych po 24 września 2018 r. 

Przed włączeniem tej funkcji, sprawdź, czy usługi domenowe Azure AD został pomyślnie wdrożony dla podstawowego dzierżawy usługi Azure AD, z którą jest skojarzone konta magazynu. Jeśli usługi domenowe Azure AD nie zostały jeszcze skonfigurowany, wykonaj instrukcje krok po kroku ze wskazówkami zawartymi w [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/create-instance.md).

Wdrożenie usługi Azure AD Domain Services zwykle trwa 10 do 15 minut. Po wdrożeniu usługi Azure AD Domain Services dla usługi Azure Files można włączyć uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB. Aby uzyskać więcej informacji, zobacz [uwierzytelniania włączyć usługi Azure Active Directory domeny za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Skonfiguruj uprawnienia na poziomie udziału plików platformy Azure
Po włączeniu usług domenowych Azure AD authentication można skonfigurować niestandardowe role RBAC dla tożsamości usługi Azure AD i przypisania praw dostępu do żadnych udziałów plików na koncie magazynu.

Aplikacji uruchomionej na maszynie Wirtualnej przyłączone do domeny próbuje zainstalować udział plików platformy Azure lub dostępu do pliku lub katalogu, aby zapewnić odpowiednie uprawnienia na poziomie udziału i uprawnień systemu plików NTFS są weryfikowane poświadczeń usługi Azure AD aplikacji. Aby uzyskać informacje o konfigurowaniu uprawnień na poziomie udziału, zobacz [włączyć usługi Azure Active Directory domeny uwierzytelniania za pośrednictwem protokołu SMB (wersja zapoznawcza)](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie pliku lub katalogu dla usługi Azure Files 
Usługa pliki Azure wymusza standardowe uprawnienia NTFS pliku, na poziomie katalogów i plików, w tym w katalogu głównym. Konfiguracja uprawnień na poziomie pliku lub katalogu jest obsługiwany tylko przez protokół SMB. Instalowanie docelowego udziału plików z maszyny Wirtualnej i skonfigurować uprawnienia za pomocą Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) lub [listy ACL zestaw](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) polecenia. 

> [!NOTE]
> Skonfigurowanie uprawnień systemu plików NTFS, za pomocą Eksploratora plików Windows nie jest obsługiwana w wersji zapoznawczej.

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Użyj klucza konta magazynu, aby uzyskać uprawnienia administratora 
Użytkownik posiada klucza konta magazynu dostęp usługi Azure Files z uprawnieniami administratora. Uprawnienia administratora pozwoliło wszystkich ograniczeń kontroli dostępu skonfigurowana na poziomie udziału, przy użyciu RBAC i wymuszane przez usługę Azure AD. Uprawnienia administratora są wymagane, aby zainstalować udział plików platformy Azure. 

> [!IMPORTANT]
> W ramach najlepsze rozwiązania dotyczące zabezpieczeń Unikaj udostępniania kluczy konta magazynu i korzystać z uprawnień usługi Azure AD, jeśli to możliwe.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Zachowaj ACL katalogów i plików do importowania danych do udziałów plików platformy Azure
Usługa pliki systemu Azure obsługuje teraz zachowywanie listy ACL katalogu lub pliku, podczas kopiowania danych do udziałów plików platformy Azure. Możesz skopiować listy ACL na katalog lub plik do usługi Azure Files. Na przykład, można użyć [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) z flagą `/copy:s` do kopiowania danych i listy kontroli dostępu do udziału plików platformy Azure. Listy ACL konserwacji jest domyślnie włączone i nie trzeba jawnie włączyć funkcję uwierzytelniania usług domenowych Azure AD w ramach konta magazynu. 

## <a name="pricing"></a>Cennik
Brak bez dodatkowej opłaty usługi, aby włączyć uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB na swoim koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [cennika usługi Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) i [cennika usługi Azure AD Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/) stron.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure Files i Azure AD przy użyciu protokołu SMB zobacz następujące zasoby:

- [Wprowadzenie do usługi Azure Files](storage-files-introduction.md)
- [Włącz uwierzytelnianie usługi Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](storage-files-active-directory-enable.md)
- [Często zadawane pytania](storage-files-faq.md)
