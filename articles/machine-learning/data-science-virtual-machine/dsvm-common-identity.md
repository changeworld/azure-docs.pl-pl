---
title: Konfiguracja wspólną tożsamością dla danych nauki maszyny wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Instalator wspólną tożsamość w środowiskach DSVM zespołu przedsiębiorstwa.
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 70c6b8cd147cefaa3128bc1e6a414a6fa61dba6d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830898"
---
# <a name="setup-common-identity-on-the-data-science-vm"></a>Instalator wspólną tożsamość na maszynie Wirtualnej nauki danych

Domyślnie na maszynie Wirtualnej Azure, w tym użytkowników lokalnych danych nauki maszyny Wirtualnej (DSVM) podczas inicjowania obsługi administracyjnej maszyny Wirtualnej są tworzone konta i uwierzytelniania użytkowników do maszyny Wirtualnej przy użyciu tych poświadczeń. Jeśli masz wiele maszyn wirtualnych, które należy uzyskać dostęp do tej metody może szybko uzyskać skomplikowane do zarządzania poświadczeniami. Typowych kont użytkowników i zarządzania przy użyciu dostawcy tożsamości oparte na standardach umożliwia użycie jednego zestawu poświadczeń dostępu do wielu zasobów na platformie Azure, łącznie z wielu DSVMs. 

Active Directory (AD) dostawcy popularnych tożsamości i jest obsługiwana zarówno na platformie Azure jako usługa, jak i lokalnie. Można wykorzystać AD lub Azure AD w celu uwierzytelniania użytkowników na autonomicznych maszyn wirtualnych nauki danych (DSVM) lub w klastrze DSVM na podstawie zestawu skali maszyny wirtualnej platformy Azure. Jest to realizowane przez dołączenie wystąpień DSVM do domeny usługi AD. Jeśli masz już usługi Active Directory do zarządzania tożsamościami, można użyć jej jako wspólne dostawcy tożsamości. W przypadku, gdy nie ma usługi AD, można wykonać AD zarządzanych na platformie Azure za pośrednictwem usługi o nazwie [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)(Azure AD DS). 

W dokumentacji [usługi Azure Active directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/) zapewnia szczegółowe [instrukcje](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity) zarządzać active directory łącznie z usługi Azure AD do katalogu lokalnego, jeśli istnieje. 

Dalszej części tego artykułu opisano kroki, aby skonfigurować w pełni zarządzana usługa domeny AD na platformie Azure przy użyciu usługi Azure AD DS, a następnie dołącz DSVMs Twojego do domeny zarządzanej AD, aby umożliwić użytkownikom dostęp do puli DSVMs (i innych zasobów platformy Azure), za pomocą wspólnego konta użytkownika i poświadczeń. 

##  <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurowanie domeny pełni zarządzanej usługi Active Directory na platformie Azure

Azure AD DS ułatwia zarządzanie tożsamościami, podając w pełni zarządzana usługa na platformie Azure. W tej domenie usługi Active directory użytkowników i grup zarządzania.  Kroki umożliwiające konfigurowanie platformy Azure hostowanej domeny usługi AD i są konta użytkowników w katalogu:

1. Dodawanie użytkowników do usługi Active directory w portalu 

    a. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
    
    b. Wybierz **usługi Azure Active Directory** , a następnie **użytkowników i grup**.
    
    c. Na **użytkowników i grup**, wybierz pozycję **wszyscy użytkownicy**, a następnie wybierz **nowego użytkownika**.
   
   ![Dodaj polecenie](./media/add-user.png)
    
    d. Wprowadź szczegóły użytkownika, takich jak **nazwa** i **nazwy użytkownika**. Część nazwy domeny nazwa użytkownika musi być początkowej domyślnej domeny nazwa "[nazwa domeny].onmicrosoft.com" lub zweryfikowane, niefederacyjnych [niestandardowej nazwy domeny](../../active-directory/add-custom-domain.md) takich jak "contoso.com".
    
    e. Skopiuj lub w przeciwnym razie należy pamiętać wygenerowane hasło, dzięki czemu można udostępnić go użytkownikowi po zakończeniu tego procesu.
    
    f. Opcjonalnie można otwierać i Wypełnij informacje w **profilu**, **grup**, lub **roli katalogu** dla użytkownika. 
    
    g. Na **użytkownika**, wybierz pozycję **Utwórz**.
    
    h. Bezpiecznie dystrybucji wygenerowane hasło dla nowego użytkownika, dzięki czemu użytkownicy mogą się logować.

2.  Tworzenie usług domenowych Azure AD

    Aby utworzyć DODAJE Azure, postępuj zgodnie z instrukcjami w artykule "[włączyć usługi Azure Active Directory Domain Services przy użyciu portalu Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started)" (zadań od 1 do 5 zadania). Należy pamiętać, że istniejące hasła użytkownika w usłudze Active directory są aktualizowane tak, aby hasła w usłudze Azure AD DS są zsynchronizowane. Należy również dodać DNS do usługi Azure AD DS wymienionych zadań #4 powyżej artykułu. 

3.  Należy utworzyć oddzielne podsieci DSVM w sieci wirtualnej utworzone w zadaniu 2 # kroku poprzedzających
4.  Utwórz co najmniej jedno wystąpienie maszyny Wirtualnej nauki danych w podsieci DSVM 
5.  Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) dodać DSVM do usługi AD. 
6.  Następnie zainstaluj udostępnionych plików Azure do hostowania katalogu głównej lub notes umożliwiające instalowanie obszaru roboczego na dowolnym komputerze. (Aby uzyskać uprawnienia na poziomie pliku ścisłej należy NFS z systemem w przynajmniej jednej maszyny wirtualnej)

    a. [Tworzenie udziału plików na platformę Azure](../../storage/files/storage-how-to-create-file-share.md)
    
    b. Zainstaluj ją na DSVM systemu Linux. Po kliknięciu przycisk "Połącz" dla plików Azure na koncie magazynu w portalu Azure, polecenie do uruchomienia w powłoki bash na Linux DSVM będą wyświetlane. Polecenie będzie wyglądać następująco:
```
sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
```
7.  Powiedz, zainstalować pliki Azure /data/workspace. Teraz utworzyć katalogów dla poszczególnych użytkowników w udziale. /Data/Workspace/User1, /data/workspace/user2 i tak dalej. Utwórz ```notebooks``` katalogu, w obszarze roboczym każdego użytkownika. 
8. Tworzenie łącza symbolicznego ```notebooks``` w ```$HOME/userx/notebooks/remote```.   

Teraz Poproś użytkowników w usłudze active directory hostowanej na platformie Azure i można zalogować się do żadnych DSVM (SSH, Jupyterhub), który jest przyłączony do usługi Azure AD DS przy użyciu poświadczeń usługi AD. Ponieważ obszar roboczy użytkownika znajduje się na udostępnionych plików Azure, użytkownik ma dostęp do swoich notesów i inne zadania z dowolnego DSVM, używając Jupyterhub. 

Do automatycznego skalowania można użyć skali maszyny wirtualnej ustawić tworzenie puli maszyn wirtualnych, które należą do domeny w taki sposób, jak i z udostępnionego dysku, zainstalowane. Użytkownicy mogą zalogować się do dowolnej maszyny dostępne w zestawie skalowania maszyn wirtualnych i mieć dostęp do udostępnionego dysku, na którym są zapisywane ich notesów. 

## <a name="next-steps"></a>Kolejne kroki

* [Bezpiecznie przechowywać poświadczenia dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)



