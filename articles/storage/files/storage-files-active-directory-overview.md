---
title: Omówienie — autoryzacja oparta na tożsamościach plików Azure
description: Usługa Azure Files obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem przystawki SMB (Server Message Block) za pośrednictwem usług domenowych Active Directory (AD DS) i usługi Active Directory. Maszyny wirtualne systemu Windows przyłączone do domeny mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.openlocfilehash: 737cdfaddca3a5f7532620bdafd86149e4d61f9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061069"
---
# <a name="overview-of-azure-files-identity-based-authentication-support-for-smb-access"></a>Omówienie obsługi uwierzytelniania opartego na tożsamościach usługi Azure Files dla dostępu do SMB
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby dowiedzieć się, jak włączyć uwierzytelnianie usługi AD dla udziałów plików platformy Azure, zobacz [Włączanie uwierzytelniania usługi Active Directory za pomocą SMB dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md).

Aby dowiedzieć się, jak włączyć uwierzytelnianie usług Azure AD DS dla udziałów plików platformy Azure, zobacz [Włączanie uwierzytelniania usług domenowych Usługi active directory azure w plikach platformy Azure](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Słownik 
Warto zrozumieć niektóre kluczowe terminy związane z uwierzytelnianiem usługi domenowej usługi Azure AD za pomocą kontrolera SMB dla udziałów plików platformy Azure:

-   **Uwierzytelnianie Kerberos**

    Kerberos to protokół uwierzytelniania używany do weryfikacji tożsamości użytkownika lub hosta. Aby uzyskać więcej informacji na temat protokołu Kerberos, zobacz [Omówienie uwierzytelniania Kerberos](https://docs.microsoft.com/windows-server/security/kerberos/kerberos-authentication-overview).

-  **Protokół bloku komunikatów serwera (SMB)**

    SMB jest standardowym w branży protokołem do udostępniania plików sieciowych. SMB jest również znany jako wspólny system plików internetowych lub CIFS. Aby uzyskać więcej informacji na temat protokołu SMB, zobacz [Omówienie protokołu Microsoft SMB protocol i CIFS Protocol Overview](https://docs.microsoft.com/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Usługa Azure Active Directory (Usługa Azure AD)**

    Usługa Azure Active Directory (Azure AD) to usługa zarządzania katalogami i tożsamościami opartymi na wielu dzierżawach firmy Microsoft. Usługa Azure AD łączy podstawowe usługi katalogowe, zarządzanie dostępem do aplikacji i ochronę tożsamości w jednym rozwiązaniu. Usługa Azure AD umożliwia maszynom wirtualnym systemu Windows przyłączonych do domeny (VM) dostęp do udziałów plików platformy Azure za pomocą poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Usługi domenowe usługi Azure AD (usługi Azure AD DS)**

    Usługi domenowe usługi Azure AD (GA) zapewniają usługi domeny zarządzanej, takie jak dołączanie do domeny, zasady grupy, ldap i uwierzytelnianie Kerberos/NTLM. Usługi te są w pełni zgodne z usługą Windows Server Active Directory. Aby uzyskać więcej informacji, zobacz [Usługi domenowe usługi w usłudze Azure Active Directory (AD).](../../active-directory-domain-services/overview.md)

- **Usługi domenowe Active Directory (AD DS, nazywane również usługami AD)**

    Usługa Active Directory (PREVIEW) udostępnia metody przechowywania danych katalogu podczas udostępniania ich użytkownikom sieci i administratorom. Zabezpieczenia są zintegrowane z usługą Active Directory poprzez uwierzytelnianie logowania i kontrolę dostępu do obiektów w katalogu. Dzięki jednemu logowaniu sieci administratorzy mogą zarządzać danymi katalogowymi i organizacją w całej swojej sieci, a autoryzowani użytkownicy sieci mogą uzyskiwać dostęp do zasobów w dowolnym miejscu w sieci. Usługa AD jest powszechnie przyjmowana przez przedsiębiorstwa w środowisku lokalnym i używa poświadczeń usługi AD jako tożsamości dla kontroli dostępu. Aby uzyskać więcej informacji, zobacz [Omówienie Usług domenowych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Kontrola dostępu oparta na rolach platformy Azure (RBAC)**

    Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla platformy Azure. Za pomocą funkcji RBAC można zarządzać dostępem do zasobów, przyznając użytkownikom najmniejszą najmniejszą uprawnienia potrzebne do wykonywania zadań. Aby uzyskać więcej informacji na temat RBAC, zobacz [Co to jest kontrola dostępu oparta na rolach (RBAC) na platformie Azure?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Typowe przypadki użycia

Uwierzytelnianie oparte na tożsamości i obsługa list ACL systemu Windows w usługach Azure Files najlepiej wykorzystać w następujących przypadkach użycia:

### <a name="replace-on-premises-file-servers"></a>Zastępowanie lokalnych serwerów plików

Przestarzałe i zastępowanie rozproszonych lokalnych serwerów plików jest częstym problemem, który napotyka każde przedsiębiorstwo w trakcie modernizacji it. Udziały plików platformy Azure z uwierzytelnianiem usługi AD (w wersji zapoznawczej) najlepiej pasują w tym miejscu, kiedy można migrować dane do usługi Azure Files. Pełna migracja pozwoli ci korzystać z wysokiej dostępności i możliwości skalowalności, a jednocześnie zminimalizować zmiany po stronie klienta, szczególnie skomplikowaną infrastrukturę domeny usługi AD. Zapewnia bezproblemową migrację do użytkowników końcowych, dzięki czemu mogą oni nadal uzyskiwać dostęp do swoich danych przy użyciu tych samych poświadczeń przy użyciu istniejących komputerów przyłączonych do domeny.

### <a name="lift-and-shift-applications-to-azure"></a>Przenoszenie i przenoszenie aplikacji na platformę Azure

Gdy "lift and shift" aplikacje do chmury, chcesz zachować ten sam model uwierzytelniania dla danych. W miarę rozszerzania środowiska kontroli dostępu opartego na tożsamości na udziały plików platformy Azure eliminuje konieczność zmiany aplikacji na nowoczesne metody eru i przyspieszenia wdrażania chmury. Udziały plików platformy Azure zapewnia możliwość integracji z usługą Azure AD DS (GA) lub AD (wersja zapoznawcza) do uwierzytelniania. Jeśli plan ma być w 100% natywny dla chmury i zminimalizować nakłady na zarządzanie infrastrukturą chmury, usługi Azure AD DS będą lepiej pasować jako w pełni zarządzana usługa domeny. Jeśli potrzebujesz pełnej zgodności z możliwościami usług AD DS (GA), warto rozważyć rozszerzenie środowiska usługi AD na chmurę przez kontrolery domeny hostowania samodzielnie na maszynach wirtualnych. Tak czy inaczej, zapewniamy elastyczność wyboru usług domenowych, które odpowiadają Twoim potrzebom biznesowym.

### <a name="backup-and-disaster-recovery-dr"></a>Tworzenie kopii zapasowych i odzyskiwanie po awarii (DR)

Jeśli przechowujesz podstawowy magazyn plików lokalnie, udziały plików platformy Azure mogą służyć jako idealny magazyn do tworzenia kopii zapasowych lub odzyskiwania po awarii, aby poprawić ciągłość działania. Za pomocą udziałów plików platformy Azure można użyć do utworzenia kopii zapasowej danych z istniejących serwerów plików przy zachowaniu dacl systemu Windows. W scenariuszach odzyskiwania po awarii można skonfigurować opcję uwierzytelniania, aby obsługiwać prawidłowe wymuszanie kontroli dostępu w trybie failover.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

W poniższej tabeli podsumowano obsługiwane scenariusze uwierzytelniania udziałów plików platformy Azure dla usług Azure AD DS (GA) i AD (wersja zapoznawcza). Firma Microsoft zaleca wybranie usługi domeny, która została przyjęta dla środowiska klienta do integracji z usługą Azure Files. Jeśli masz usługę AD (wersja zapoznawcza) już skonfigurowaną lokalnie lub na platformie Azure, gdzie twoje urządzenia są domeną przyłączone do usługi AD, należy wybrać opcję wykorzystania uwierzytelniania udziałów plików usługi AD (preview) dla udziału plików platformy Azure. Podobnie jeśli zostały już przyjęte usługi Azure AD DS (GA), należy użyć tego dla uwierzytelniania udziałów plików platformy Azure.


|Uwierzytelnianie usług Azure AD DS (GA)  |Uwierzytelnianie usługi AD (w wersji zapoznawczej)  |
|---------|---------|
|Przyłączone do domeny usług Azure AD DS maszyny z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure za pomocą poświadczeń usługi Azure AD za pomocą kontrolera SMB.     |Maszyny z systemem Windows przyłączone do domeny usługi AD mogą uzyskiwać dostęp do udziałów plików platformy Azure za pomocą poświadczeń usługi AD, które są zsynchronowane z usługą Azure AD za pomocą kontrolera SMB.         |

### <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

- Uwierzytelnianie usługi Azure AD DS (GA) i AD (wersja zapoznawcza) nie obsługuje uwierzytelniania na kontach komputerów. Zamiast tego można rozważyć użycie konta logowania do usługi.
- Uwierzytelnianie usługi Azure AD DS (GA) nie obsługuje uwierzytelniania na urządzeniach przyłączonych do chmury usługi Azure AD.

## <a name="advantages-of-identity-based-authentication"></a>Zalety uwierzytelniania opartego na tożsamości
Uwierzytelnianie oparte na tożsamości dla usług Azure Files oferuje kilka korzyści w związku z korzystaniem z uwierzytelniania klucza udostępnionego:

-   **Rozszerzanie tradycyjnego środowiska dostępu do udostępniania plików opartego na tożsamości do chmury dzięki usługom AD i usłudze Azure AD DS**  
    Jeśli planujesz "lift and shift" aplikacji do chmury, zastępując tradycyjnych serwerów plików z udziałami plików platformy Azure, a następnie może być aplikacja do uwierzytelniania za pomocą poświadczeń usługi AD lub usługi Azure AD, aby uzyskać dostęp do danych plików. Usługa Azure Files obsługuje przy użyciu poświadczeń usługi AD lub usługi Azure AD w celu uzyskania dostępu do udziałów plików platformy Azure za pośrednictwem kontrolera SMB z maszyn wirtualnych przyłączonych do domeny usług AD lub Azure AD DS.

-   **Wymuszanie szczegółowej kontroli dostępu w udziałach plików platformy Azure**  
    Uprawnienia do określonej tożsamości można przyznać na poziomie udziału, katalogu lub pliku. Załóżmy na przykład, że masz kilka zespołów przy użyciu pojedynczego udziału plików platformy Azure do współpracy z projektem. Można udzielić wszystkim zespołom dostępu do niewrażliwych katalogów, ograniczając jednocześnie dostęp do katalogów zawierających poufne dane finansowe tylko dla zespołu finansów. 

-   **Łań 100 000 000 000 użytkowników systemu Windows (znanych również jako ntfs) wraz z danymi**  
    Za pomocą udziałów plików platformy Azure można użyć do utworzenia kopii zapasowej istniejących lokalnych udziałów plików. Usługa Azure Files zachowuje listy ACL wraz z danymi podczas 30-10 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 0

## <a name="how-it-works"></a>Jak to działa
Udziały plików platformy Azure obsługuje uwierzytelnianie kerberos do integracji z usługą Azure AD DS (GA) lub AD (wersja zapoznawcza). Aby włączyć uwierzytelnianie w udziałach plików platformy Azure, należy najpierw skonfigurować środowisko domeny. W przypadku uwierzytelniania usług Azure AD DS (GA) należy włączyć usługi domenowe usługi ad usługi Azure i domeny dołączyć do maszyn wirtualnych, które mają mieć dostęp do danych plików. Maszyna wirtualna przyłączona do domeny musi znajdować się w tej samej sieci wirtualnej (VNET) co usługi domenowe usługi Azure AD Domain Services. Podobnie w przypadku uwierzytelniania usługi AD (w wersji zapoznawczej) należy skonfigurować kontroler domeny usługi Active Directory i domenę dołączyć do komputerów lub maszyn wirtualnych.

Gdy tożsamość skojarzona z aplikacją działającą na maszynie wirtualnej próbuje uzyskać dostęp do danych w udziałach plików platformy Azure, żądanie jest wysyłane do usług domenowych usługi Azure AD w celu uwierzytelnienia tożsamości. Jeśli uwierzytelnianie zakończy się pomyślnie, usługi domenowe usługi Azure AD zwracają token Protokołu Kerberos. Aplikacja wysyła żądanie, które zawiera token Kerberos, a udziały plików platformy Azure używają tego tokenu do autoryzowania żądania. Udziały plików platformy Azure odbiera tylko token i nie utrwalić poświadczeń usługi Azure AD. Uwierzytelnianie usługi AD działa w podobny sposób, gdzie ad udostępnia token Kerberos.

![Zrzut ekranu przedstawiający diagram uwierzytelniania usługi Azure AD za pomocą SMB](media/storage-files-active-directory-overview/azure-active-directory-over-smb-for-files-overview.png)

### <a name="enable-identity-based-authentication"></a>Włączanie uwierzytelniania opartego na tożsamości

Uwierzytelnianie oparte na tożsamości można włączyć za pomocą usługi Azure AD DS (GA) lub AD (wersja zapoznawcza) dla udziałów plików platformy Azure na nowych i istniejących kontach magazynu. Tylko jedna usługa domeny może być używana do uwierzytelniania dostępu do plików na koncie magazynu, który ma zastosowanie do wszystkich udziałów plików na koncie. Szczegółowe wskazówki krok po kroku dotyczące konfigurowania udziałów plików do uwierzytelniania za pomocą usług Azure AD DS (GA) w naszym artykule [Włączanie uwierzytelniania usług domenowych Usługi Active Directory w usłudze Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) oraz wskazówki dotyczące usługi AD (wersja zapoznawcza) w innym artykule [Włącz uwierzytelnianie usługi Active Directory za pomocą SMB dla udziałów plików platformy Azure.](storage-files-identity-auth-active-directory-enable.md)

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień na poziomie udziału dla usług Azure Files

Po włączeniu uwierzytelniania usług Azure AD DS (GA) lub AD (wersja zapoznawcza) można użyć wbudowanych ról RBAC lub skonfigurować role niestandardowe dla tożsamości usługi Azure AD i przypisać prawa dostępu do dowolnych udziałów plików na kontach magazynu. przypisane uprawnienie umożliwia przyznanej tożsamości, aby uzyskać dostęp tylko do udziału, nic więcej, nawet katalogu głównego. Nadal trzeba oddzielnie skonfigurować uprawnienia katalogu lub na poziomie pliku dla udziałów plików platformy Azure.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurowanie uprawnień katalogu lub na poziomie pliku dla usług Azure Files

Udziały plików platformy Azure wymuszają standardowe uprawnienia do plików systemu Windows zarówno na poziomie katalogu, jak i pliku, w tym katalogu głównego. Konfiguracja uprawnień katalogu lub na poziomie pliku jest obsługiwana zarówno przez SMB, jak i REST. Zainstaluj docelowy udział plików z maszyny Wirtualnej i skonfiguruj uprawnienia przy użyciu Eksploratora plików systemu Windows, [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)systemu Windows lub polecenia [Set-ACL.](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl?view=powershell-6)

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Używanie klucza konta magazynu dla uprawnień superużytnika

Użytkownik posiadający klucz konta magazynu może uzyskać dostęp do udziałów plików platformy Azure z uprawnieniami superużytkownika. Uprawnienia superużytnika pomijają wszystkie ograniczenia kontroli dostępu.

> [!IMPORTANT]
> Zalecaną najlepszą praktyką w zakresie zabezpieczeń jest unikanie udostępniania kluczy konta magazynu i w miarę możliwości korzystanie z uwierzytelniania opartego na tożsamości.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Zachowywanie list ACL katalogu i plików podczas importowania danych do udziałów plików platformy Azure

Usługa Azure Files obsługuje zachowywanie list ACL na poziomie katalogu lub pliku podczas kopiowania danych do udziałów plików platformy Azure. Listy ACL w katalogu lub pliku można skopiować do udziałów plików platformy Azure przy użyciu usługi Azure File Sync lub wspólnych zestawów narzędzi przenoszenia plików. Na przykład można użyć [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) z flagą `/copy:s` do kopiowania danych, a także list ACL do udziału plików platformy Azure. Listy ACL są domyślnie zachowywane, nie jest wymagane, aby włączyć uwierzytelnianie oparte na tożsamości na koncie magazynu, aby zachować listy ACL.

## <a name="pricing"></a>Cennik
Nie ma żadnych dodatkowych opłat za usługę, aby włączyć uwierzytelnianie oparte na tożsamości za pomocą SMB na koncie magazynu. Aby uzyskać więcej informacji na temat cen, zobacz [Ceny usługi Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) i strony cen usługi Azure AD Domain [Services,](https://azure.microsoft.com/pricing/details/active-directory-ds/) jeśli szukasz informacji AAD DS.

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat plików platformy Azure i uwierzytelniania opartego na tożsamości za pomocą SMB, zobacz następujące zasoby:

- [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md)
- [Włączanie uwierzytelniania usługi Active Directory za pomocą smb dla udziałów plików platformy Azure](storage-files-identity-auth-active-directory-enable.md)
- [Włączanie uwierzytelniania usług domenowych Active Directory platformy Azure w plikach platformy Azure](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Najczęściej zadawane pytania](storage-files-faq.md)
