---
title: Przegląd — Azure Files Autoryzacja oparta na tożsamościach
description: Azure Files obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem Azure Active Directory Domain Services (AD DS) i Active Directory. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 3a9a2a903bd9979cd0f9a09b7589edc6d4fd8962
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565092"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Omówienie usługi Azure Files uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć uwierzytelnianie usługi AD dla udziałów plików platformy Azure, zobacz [Włączanie uwierzytelniania Active Directory za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-active-directory-domain-services-enable.md).

Aby dowiedzieć się, jak włączyć uwierzytelnianie AD DS platformy Azure dla udziałów plików platformy Azure, zobacz [włączanie Azure Active Directory uwierzytelniania usługi domeny za pośrednictwem protokołu SMB dla Azure Files](storage-files-active-directory-enable.md).

## <a name="glossary"></a>Słownik 
Warto zrozumieć niektóre kluczowe terminy związane z uwierzytelnianiem w usłudze Azure AD Domain Service za pośrednictwem protokołu SMB dla udziałów plików platformy Azure:

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania używany do weryfikacji tożsamości użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz temat [uwierzytelnianie Kerberos — Omówienie](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół bloku komunikatów serwera (SMB)**

    SMB jest standardowym protokołem udostępniania plików w branży. Protokół SMB jest również znany jako wspólny internetowy system plików lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [Omówienie protokołów SMB i CIFS firmy Microsoft](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) to usługa firmy Microsoft do zarządzania katalogami i tożsamościami opartymi na chmurze. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu. Usługa Azure AD umożliwia przyłączonych do domeny maszynom wirtualnym z systemem Windows dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure AD Domain Services (AD DS platformy Azure)**

    Azure AD Domain Services (GA) oferuje zarządzane usługi domenowe, takie jak przyłączanie do domeny, zasady grupy, LDAP i uwierzytelnianie Kerberos/NTLM. Te usługi są w pełni zgodne z systemem Windows Server Active Directory. Aby uzyskać więcej informacji, zobacz [usługi domenowe Azure Active Directory (AD)](../../active-directory-domain-services/overview.md).

- **Active Directory Domain Services (AD DS, określane również jako AD)**

    Usługa Active Directory (AD) (wersja zapoznawcza) udostępnia metody przechowywania danych katalogowych, a jednocześnie udostępnia je użytkownikom i administratorom sieci. Zabezpieczenia są zintegrowane z Active Directory za pomocą uwierzytelniania logowania i kontroli dostępu do obiektów w katalogu. Za pomocą pojedynczego logowania do sieci administratorzy mogą zarządzać danymi katalogowymi i organizacją w całej sieci, a autoryzowani użytkownicy sieci mogą uzyskiwać dostęp do zasobów w dowolnym miejscu w sieci. Usługi AD są powszechnie wdrażane przez przedsiębiorstwa w środowisku lokalnym i używają poświadczeń usługi AD jako tożsamości kontroli dostępu. Aby uzyskać więcej informacji, zobacz [Active Directory Domain Services przegląd](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Access Control oparte na rolach (RBAC) na platformie Azure**

    Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Za pomocą RBAC można zarządzać dostępem do zasobów, przyznając użytkownikom najmniejsze uprawnienia potrzebne do wykonywania swoich zadań. Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure?](../../role-based-access-control/overview.md)

## <a name="common-use-cases"></a>Typowe przypadki użycia

Uwierzytelnianie oparte na tożsamości i pomoc techniczna dla list ACL systemu Windows w Azure Files jest najlepiej wykorzystane w następujących przypadkach użycia:

### <a name="replace-on-premises-file-servers"></a>Zastępowanie lokalnych serwerów plików

Przestarzałe i zastępowane lokalne serwery plików są typowym problemem, który każde przedsiębiorstwo napotyka w trakcie jego modernizacji. Udziały plików platformy Azure z uwierzytelnianiem za pomocą usługi AD (wersja zapoznawcza) są najlepszym rozwiązaniem w tym miejscu, gdy można migrować dane do Azure Files. Pełna migracja umożliwi skorzystanie z zalet wysokiej dostępności i skalowalności, jednocześnie minimalizując zmiany po stronie klienta, szczególnie skomplikowaną infrastrukturę domeny usługi AD. Zapewnia bezproblemowe środowisko migracji użytkownikom końcowym, dzięki czemu może nadal uzyskiwać dostęp do danych za pomocą tych samych poświadczeń przy użyciu istniejących maszyn przyłączonych do domeny.

### <a name="lift-and-shift-applications-to-azure"></a>Podnieś i Przenieś aplikacje na platformę Azure

W przypadku aplikacji "Unieś i Przenieś" do chmury chcesz zachować ten sam model uwierzytelniania dla danych. W miarę jak rozszerzono środowisko kontroli dostępu oparte na tożsamościach do udziałów plików platformy Azure, eliminuje konieczność zmiany aplikacji na nowoczesne metody uwierzytelniania i przyspieszanie wdrażania chmury. Udziały plików platformy Azure udostępniają opcję integracji z usługą Azure AD DS (GA) lub AD (wersja zapoznawcza) na potrzeby uwierzytelniania. Jeśli plan ma być 100% w chmurze i minimalizuje wysiłki związane z zarządzaniem infrastrukturami w chmurze, platforma Azure AD DS będzie lepiej dopasowana jako w pełni zarządzana usługa domeny. Jeśli potrzebujesz pełnej zgodności z możliwościami AD DS (GA), możesz rozważyć rozszerzenie środowiska usługi AD na chmurę przez samoobsługowe kontrolery domeny na maszynach wirtualnych. W obu przypadkach zapewniamy elastyczność wybierania usług domenowych, które odpowiadają potrzebom biznesowym.

### <a name="backup-and-disaster-recovery-dr"></a>Kopia zapasowa i odzyskiwanie po awarii (DR)

Jeśli przechowujesz podstawowy magazyn plików lokalnie, udziały plików platformy Azure mogą działać jako idealny magazyn do tworzenia kopii zapasowych i odzyskiwania po awarii, aby zwiększyć ciągłość działania firmy. Udziałów plików platformy Azure można używać do tworzenia kopii zapasowych danych z istniejących serwerów plików, zachowując jednocześnie listy DACL systemu Windows. W przypadku scenariuszy odzyskiwania po awarii można skonfigurować opcję uwierzytelniania, która będzie obsługiwać odpowiednie wymuszanie kontroli dostępu w trybie failover.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Poniższa tabela zawiera podsumowanie obsługiwanych scenariuszy uwierzytelniania udziałów plików platformy Azure dla usługi Azure AD DS (GA) i usługi AD (wersja zapoznawcza). Zalecamy wybranie usługi domeny, która została przyjęta dla środowiska klienta na potrzeby integracji z Azure Files. Jeśli masz już skonfigurowaną usługę AD (wersja zapoznawcza) lokalnie lub na platformie Azure, w której urządzenia są przyłączone do usługi AD, należy wybrać opcję korzystania z usługi AD (wersja zapoznawcza) na potrzeby uwierzytelniania udziałów plików platformy Azure. Podobnie, jeśli już przyjęto usługę Azure AD DS (GA), należy ją użyć do uwierzytelniania udziałów plików platformy Azure.


|Uwierzytelnianie na platformie Azure AD DS (GA)  |Uwierzytelnianie usługi AD (wersja zapoznawcza)  |
|---------|---------|
|Komputery z systemem Windows przyłączone do domeny AD DS mogą uzyskać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD przy użyciu protokołu SMB.     |Komputery z systemem Windows przyłączone do domeny usługi Active Directory mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi AD, które są zsynchronizowane z usługą Azure AD przez         |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

- Uwierzytelnianie za pomocą usługi Azure AD DS (GA) i AD (wersja zapoznawcza) nie obsługują uwierzytelniania dla kont komputerów. Zamiast tego możesz rozważyć użycie konta logowania do usługi.
- Uwierzytelnianie za pomocą usługi Azure AD DS (GA) nie obsługuje uwierzytelniania w odniesieniu do urządzeń przyłączonych do chmury usługi Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Zalety uwierzytelniania opartego na tożsamościach
Uwierzytelnianie oparte na tożsamościach dla Azure Files oferuje kilka korzyści z używania uwierzytelniania klucza współużytkowanego:

-   **Rozpoznaj tradycyjne środowisko dostępu do udziałów plików oparte na tożsamościach w chmurze za pomocą usług AD i Azure AD DS**  
    Jeśli planujesz "podnieść i przesuniesz" aplikację do chmury, zastępując tradycyjne serwery plików z udziałami plików platformy Azure, możesz chcieć, aby aplikacja mogła uwierzytelniać się przy użyciu poświadczeń usługi AD lub Azure AD w celu uzyskania dostępu do danych plików. Azure Files obsługuje używanie poświadczeń usługi AD lub Azure AD w celu uzyskiwania dostępu do udziałów plików platformy Azure za pośrednictwem protokołu SMB z usług AD lub Azure AD DS maszyn wirtualnych przyłączonych do domeny.

-   **Wymuś szczegółową kontrolę dostępu w udziałach plików platformy Azure**  
    Można przyznać uprawnienia do określonej tożsamości na poziomie udziału, katalogu lub pliku. Załóżmy na przykład, że masz kilka zespołów korzystających z jednego udziału plików platformy Azure do współpracy z projektem. Można udzielić wszystkim zespołom dostępu do niewrażliwych katalogów, ograniczając dostęp do katalogów zawierających poufne dane finansowe tylko do zespołu finansowego. 

-   **Tworzenie kopii zapasowych list ACL systemu Windows (znanych również jako NTFS) wraz z danymi**  
    Za pomocą udziałów plików platformy Azure można tworzyć kopie zapasowe istniejących lokalnych udziałów plików. Azure Files zachowuje listy kontroli dostępu wraz z danymi podczas tworzenia kopii zapasowej udziału plików w udziałach plików platformy Azure za pośrednictwem protokołu SMB.

## <a name="how-it-works"></a>Jak to działa
Udziały plików platformy Azure obsługują uwierzytelnianie Kerberos w celu integracji z platformą Azure AD DS (GA) lub AD (wersja zapoznawcza). Aby można było włączyć uwierzytelnianie w udziałach plików platformy Azure, należy najpierw skonfigurować środowisko domeny. W przypadku uwierzytelniania za pomocą usługi Azure AD DS (GA) należy włączyć Azure AD Domain Services i dołączanie domeny do maszyn wirtualnych, z których planujesz uzyskać dostęp do danych plików. Przyłączona do domeny maszyna wirtualna musi znajdować się w tej samej sieci wirtualnej co Azure AD Domain Services. Podobnie w przypadku uwierzytelniania za pośrednictwem usługi AD (wersja zapoznawcza) musisz skonfigurować Active Directory kontroler domeny i domenę Przyłącz się do maszyn lub maszyn wirtualnych.

Gdy tożsamość skojarzona z aplikacją działającą na maszynie wirtualnej próbuje uzyskać dostęp do danych w udziałach plików platformy Azure, żądanie jest wysyłane do Azure AD Domain Services w celu uwierzytelnienia tożsamości. W przypadku pomyślnego uwierzytelnienia Azure AD Domain Services zwraca token Kerberos. Aplikacja wysyła żądanie zawierające token Kerberos, a udziały plików platformy Azure używają tego tokenu do autoryzowania żądania. Udziały plików platformy Azure odbierają tylko tokeny i nie utrwalają poświadczeń usługi Azure AD. Uwierzytelnianie usługi AD działa w podobny sposób, gdzie AD udostępnia token Kerberos.

![Zrzut ekranu przedstawiający diagram uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Włączanie uwierzytelniania opartego na tożsamościach

Możesz włączyć uwierzytelnianie oparte na tożsamościach w przypadku udziałów plików platformy Azure w ramach nowych i istniejących kont magazynu za pomocą usługi Azure AD DS (GA) lub AD (wersja zapoznawcza). Tylko jedna usługa domeny może być używana do uwierzytelniania dostępu do plików na koncie magazynu, które ma zastosowanie do wszystkich udziałów plików na koncie. Szczegółowe instrukcje krok po kroku dotyczące konfigurowania udziałów plików do uwierzytelniania za pomocą usługi Azure AD DS (GA) w naszym artykule [umożliwiają włączenie uwierzytelniania Azure Active Directory Domain Services za pośrednictwem protokołu SMB na potrzeby Azure Files](storage-files-active-directory-enable.md) i wskazówki dotyczące usługi AD (wersja zapoznawcza) w naszym innym artykule, co [umożliwia Active Directory za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-active-directory-domain-services-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Skonfiguruj uprawnienia na poziomie udziału dla Azure Files

Po włączeniu uwierzytelniania usługi Azure AD DS (GA) lub AD (wersja zapoznawcza) można używać wbudowanych ról RBAC lub konfigurować role niestandardowe dla tożsamości usługi Azure AD i przypisywać prawa dostępu do dowolnych udziałów plików na kontach magazynu. przypisane uprawnienie umożliwia uzyskanie dostępu tylko do udziału, a nie w katalogu głównym. Nadal trzeba oddzielnie skonfigurować uprawnienia na poziomie katalogu lub pliku dla udziałów plików platformy Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie katalogu lub pliku dla Azure Files

Udziały plików platformy Azure wymuszają standardowe uprawnienia do plików systemu Windows zarówno na poziomie katalogu, jak i pliku, łącznie z katalogiem głównym. Konfiguracja uprawnień na poziomie katalogu lub pliku jest obsługiwana w przypadku protokołu SMB i REST. Zainstaluj docelowy udział plików z maszyny wirtualnej i Skonfiguruj uprawnienia za pomocą Eksploratora plików systemu Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)systemu Windows lub polecenia [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Użyj klucza konta magazynu dla uprawnień administratora

Użytkownik posiadający klucz konta magazynu może uzyskać dostęp do udziałów plików platformy Azure z uprawnieniami administratora. Uprawnienia administratora omijają wszystkie ograniczenia kontroli dostępu.

> [!IMPORTANT]
> Zalecanym najlepszym rozwiązaniem w zakresie zabezpieczeń jest uniknięcie udostępniania kluczy konta magazynu i użycie uwierzytelniania opartego na tożsamości wszędzie tam, gdzie to możliwe.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachowaj listy ACL katalogów i plików podczas importowania danych do udziałów plików platformy Azure

Azure Files obsługuje zachowanie list ACL na poziomie katalogu lub pliku podczas kopiowania danych do udziałów plików platformy Azure. Listy ACL z katalogu lub pliku można kopiować do udziałów plików platformy Azure przy użyciu Azure File Sync lub wspólnych zestawów narzędzi przenoszenia plików. Na przykład można użyć [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) z flagą `/copy:s`, aby skopiować dane, a także listy ACL do udziału plików platformy Azure. Listy ACL są domyślnie zachowywane, nie jest wymagane włączenie uwierzytelniania opartego na tożsamościach na koncie magazynu w celu zachowania list ACL.

## <a name="pricing"></a>Ceny
Nie ma dodatkowej opłaty za usługę w celu włączenia uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB na koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [Azure Files ceny](https://azure.microsoft.com/pricing/details/storage/files/) i [Azure AD Domain Services cennika](https://azure.microsoft.com/pricing/details/active-directory-ds/) , jeśli szukasz informacji usługi AAD ds.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat Azure Files i uwierzytelniania opartego na tożsamościach za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Włącz uwierzytelnianie Active Directory za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](storage-files-active-directory-domain-services-enable.md)
- [Włącz uwierzytelnianie Azure Active Directory Domain Services na Azure Files](storage-files-active-directory-enable.md)
- [Często zadawane pytania](storage-files-faq.md)
