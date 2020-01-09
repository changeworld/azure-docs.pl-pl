---
title: Przegląd — Azure AD Domain Services autoryzacja — Azure Files
description: Azure Files obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem usług domenowych Azure Active Directory (Azure AD). Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 08/07/2019
ms.author: rogarana
ms.openlocfilehash: 93db726a2cac14109e542972ce851943b290962f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460284"
---
# <a name="overview-of-azure-files-azure-active-directory-domain-service-azure-ad-ds-authentication-support-for-smb-access"></a>Omówienie obsługi uwierzytelniania protokołu SMB w AD DS usłudze Azure Files Azure Active Directory
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć uwierzytelnianie AD DS Azure dla Azure Files, zobacz [Włączanie uwierzytelniania usługi domeny Azure Active Directory za pośrednictwem protokołu SMB dla Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Słownik 
Warto zrozumieć niektóre kluczowe terminy związane z uwierzytelnianiem w usłudze Azure AD Domain Service za pośrednictwem protokołu SMB dla Azure Files:

-   **Azure Active Directory (Azure AD)**  
    Azure Active Directory (Azure AD) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu. Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services**  
    Azure AD Domain Services udostępnia zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM. Te usługi są w pełni zgodne z systemem Windows Server Active Directory. Aby uzyskać więcej informacji, zobacz [usługi domenowe Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

-   **Access Control oparte na rolach (RBAC) na platformie Azure**  
    Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Za pomocą RBAC można zarządzać dostępem do zasobów, przyznając użytkownikom najmniejsze uprawnienia potrzebne do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure?](../../role-based-access-control/overview.md)

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania używany do weryfikacji tożsamości użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz temat [uwierzytelnianie Kerberos — Omówienie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół bloku komunikatów serwera (SMB)**  
    SMB jest standardowym protokołem udostępniania plików w branży. Protokół SMB jest również znany jako wspólny internetowy system plików lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [Omówienie protokołów SMB i CIFS firmy Microsoft](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

## <a name="advantages-of-azure-ad-domain-service-authentication"></a>Zalety uwierzytelniania usługi Azure AD Domain Service
Uwierzytelnianie w usłudze Azure AD Domain Service dla Azure Files oferuje kilka korzyści z używania uwierzytelniania klucza współużytkowanego:

-   **Rozpoznaj tradycyjne środowisko dostępu do udziałów plików oparte na tożsamościach w chmurze dzięki usłudze Azure AD i usłudze Azure AD Domain Service**  
    Jeśli planujesz "Unieś i Przenieś" aplikację do chmury, zastępując tradycyjne serwery plików Azure Files, możesz chcieć, aby aplikacja mogła uwierzytelnić się przy użyciu poświadczeń usługi Azure AD w celu uzyskania dostępu do danych plików. Azure Files obsługuje korzystanie z poświadczeń usługi Azure AD w celu uzyskania dostępu Azure Files za pośrednictwem protokołu SMB z platformy Azure AD DS maszyn wirtualnych z systemem Windows Możesz również zsynchronizować wszystkie lokalne obiekty Active Directory z usługą Azure AD, aby zachować nazwy użytkowników, hasła i inne przypisania grup.

-   **Wymuś szczegółową kontrolę dostępu w udziałach plików platformy Azure**  
    Można przyznać uprawnienia do określonej tożsamości na poziomie udziału, katalogu lub pliku. Załóżmy na przykład, że masz kilka zespołów korzystających z jednego udziału plików platformy Azure do współpracy z projektem. Można udzielić wszystkim zespołom dostępu do niewrażliwych katalogów, ograniczając dostęp do katalogów zawierających poufne dane finansowe tylko do zespołu finansowego. 

-   **Twórz kopie zapasowe list ACL wraz z danymi**  
    Za pomocą Azure Files można tworzyć kopie zapasowe istniejących lokalnych udziałów plików. Azure Files zachowuje listy ACL wraz z danymi podczas tworzenia kopii zapasowej udziału plików do Azure Files za pośrednictwem protokołu SMB.

## <a name="how-it-works"></a>Zasady działania
Azure Files używa Azure AD Domain Services do obsługi uwierzytelniania Kerberos przy użyciu poświadczeń usługi Azure AD z maszyn wirtualnych przyłączonych do domeny. Aby można było używać usługi Azure AD z Azure Files, należy najpierw włączyć Azure AD Domain Services i dołączyć do domeny z maszyn wirtualnych, z których planujesz uzyskać dostęp do danych plików. Przyłączona do domeny maszyna wirtualna musi znajdować się w tej samej sieci wirtualnej co Azure AD Domain Services. 

Gdy tożsamość skojarzona z aplikacją działającą na maszynie wirtualnej próbuje uzyskać dostęp do danych w Azure Files, żądanie jest wysyłane do Azure AD Domain Services w celu uwierzytelnienia tożsamości. W przypadku pomyślnego uwierzytelnienia Azure AD Domain Services zwraca token Kerberos. Aplikacja wysyła żądanie zawierające token Kerberos, a Azure Files używa tego tokenu do autoryzowania żądania. Azure Files odbiera tylko token i nie utrwala poświadczeń usługi Azure AD.

![Zrzut ekranu przedstawiający diagram uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-azure-ad-domain-service-authentication-for-smb-access"></a>Włączanie uwierzytelniania usługi Azure AD Domain Service dla dostępu SMB
Możesz włączyć uwierzytelnianie usługi Azure AD Domain Service dla Azure Files na nowych i istniejących kontach magazynu utworzonych po 24 września 2018. 

Przed włączeniem tej funkcji Sprawdź, czy Azure AD Domain Services został wdrożony dla podstawowej dzierżawy usługi Azure AD, z którą skojarzone jest konto magazynu. Jeśli nie skonfigurowano jeszcze Azure AD Domain Services, postępuj zgodnie ze wskazówkami krok po kroku podanymi w temacie [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

Wdrożenie Azure AD Domain Services zwykle trwa od 10 do 15 minut. Po wdrożeniu Azure AD Domain Services można włączyć uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files. Aby uzyskać więcej informacji, zobacz [włączanie Azure Active Directory uwierzytelniania usługi domeny za pośrednictwem protokołu SMB dla Azure Files](storage-files-active-directory-enable.md). 

### <a name="configure-share-level-permissions-for-azure-files"></a>Skonfiguruj uprawnienia na poziomie udziału dla Azure Files
Po włączeniu uwierzytelniania usługi Azure AD Domain Service można skonfigurować niestandardowe role RBAC dla tożsamości usługi Azure AD i przypisać prawa dostępu do wszystkich udziałów plików na koncie magazynu.

Gdy aplikacja uruchomiona na maszynie wirtualnej przyłączonych do domeny próbuje zainstalować udział plików platformy Azure lub uzyskać dostęp do katalogu lub pliku, poświadczenia usługi Azure AD aplikacji są weryfikowane w celu zapewnienia odpowiednich uprawnień na poziomie udziału i uprawnień systemu plików NTFS. Informacje o konfigurowaniu uprawnień na poziomie udziału znajdują się w temacie [Włączanie uwierzytelniania Azure Active Directory usługi domeny za pośrednictwem protokołu SMB](storage-files-active-directory-enable.md).

### <a name="configure-directory--or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie katalogu lub pliku dla Azure Files 
Azure Files wymusza standardowe uprawnienia plików NTFS na poziomie katalogu i pliku, w tym w katalogu głównym. Konfiguracja uprawnień na poziomie katalogu lub pliku jest obsługiwana tylko za pośrednictwem protokołu SMB. Zainstaluj docelowy udział plików z maszyny wirtualnej i Skonfiguruj uprawnienia za pomocą Eksploratora plików systemu Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) systemu Windows lub polecenia [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Użyj klucza konta magazynu dla uprawnień administratora 
Użytkownik posiadający klucz konta magazynu może uzyskać dostęp do Azure Files z uprawnieniami administratora. Uprawnienia administratora przekraczają wszystkie ograniczenia kontroli dostępu skonfigurowane na poziomie udziału z kontrolą RBAC i wymuszane przez usługę Azure AD. Do zainstalowania udziału plików platformy Azure wymagane są uprawnienia administratora. 

> [!IMPORTANT]
> W ramach najlepszych rozwiązań dotyczących zabezpieczeń należy unikać udostępniania kluczy konta magazynu i korzystać z uprawnień usługi Azure AD wszędzie tam, gdzie to możliwe.

### <a name="preserve-directory-and-file-acls-for-data-import-to-azure-file-shares"></a>Zachowaj listy ACL katalogów i plików na potrzeby importowania danych do udziałów plików platformy Azure
Azure Files teraz obsługuje zachowanie list ACL katalogów lub plików podczas kopiowania danych do udziałów plików platformy Azure. Listy ACL można skopiować do katalogu lub pliku, aby Azure Files. Na przykład można użyć [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) z flagą `/copy:s`, aby skopiować dane i listy kontroli dostępu do udziału plików platformy Azure. Zachowywanie listy ACL jest domyślnie włączone i nie trzeba jawnie włączać funkcji uwierzytelniania usługi Azure AD Domain Service na koncie magazynu. 

## <a name="pricing"></a>Cennik
Nie ma dodatkowej opłaty za usługę w celu włączenia uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB na koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [Azure Files ceny](https://azure.microsoft.com/pricing/details/storage/files/) i [Azure AD Domain Services cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/) .

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Azure Files i uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Wprowadzenie do Azure Files](storage-files-introduction.md)
- [Włącz uwierzytelnianie Azure Active Directory za pośrednictwem protokołu SMB dla Azure Files](storage-files-active-directory-enable.md)
- [Często zadawane pytania](storage-files-faq.md)
