---
title: Konfigurowanie wspólną tożsamością dla danych nauki maszyny wirtualnej - Azure | Dokumentacja firmy Microsoft
description: Konfigurowanie wspólną tożsamość w środowiskach DSVM zespół przedsiębiorstwa.
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
ms.openlocfilehash: d6235f3a425481a13e627d683bb4c3943b473b40
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309827"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Konfigurowanie wspólną tożsamość na maszynie wirtualnej nauki danych

Na maszynie wirtualnej platformy Azure (VM), łącznie z maszyny wirtualnej nauki danych (DSVM) należy utworzyć konta użytkowników lokalnych podczas inicjowania obsługi administracyjnej maszyny Wirtualnej. Przy użyciu tych poświadczeń użytkowników uwierzytelniania do maszyny Wirtualnej. Jeśli masz wiele maszyn wirtualnych, które należy uzyskać dostęp do tej metody można szybko uzyskać skomplikowane zarządzania poświadczeniami. Typowych kont użytkowników i zarządzanie przez dostawcę tożsamości oparte na standardach umożliwiają korzystanie z jednego zestawu poświadczeń dostępu do wielu zasobów na platformie Azure, w tym wiele DSVMs. 

Usługi Active Directory jest to dostawca tożsamości popularnych i jest obsługiwany na platformie Azure jako usługę i lokalnie. Można użyć usługi Azure Active Directory (Azure AD) lub lokalnej usługi Active Directory do uwierzytelniania użytkowników w DSVM autonomicznym lub klastrze DSVMs zestawu skali maszyny wirtualnej platformy Azure. W tym celu przyłączania wystąpień DSVM do domeny usługi Active Directory. 

Jeśli masz już usługi Active Directory do zarządzania tożsamościami, można użyć jej jako wspólne dostawcy tożsamości. Nie masz usługi Active Directory, można uruchomić zarządzanych wystąpienie usługi Active Directory na platformie Azure, za pośrednictwem usługi o nazwie [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

W dokumentacji [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) zapewnia szczegółowe [instrukcje zarządzania](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution#synchronized-identity), łącznie z usługi Azure AD do katalogu lokalnego, jeśli istnieje. 

W tym artykule opisano kroki, aby skonfigurować w pełni zarządzana usługa domeny usługi Active Directory na platformie Azure przy użyciu usługi Azure AD DS. Twoje DSVMs można następnie dołączają do zarządzanej domeny usługi Active Directory, aby umożliwić użytkownikom dostęp do puli DSVMs (i innych zasobów platformy Azure) za pomocą wspólnego konta użytkownika i poświadczeń. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurowanie domeny pełni zarządzanej usługi Active Directory na platformie Azure

Azure AD DS ułatwia zarządzanie tożsamościami, podając w pełni zarządzana usługa na platformie Azure. W tej domenie usługi Active Directory możesz zarządzać użytkownikami i grupami. Dostępne są następujące kroki, aby skonfigurować hostowanymi na platformie Azure Active Directory domeny i kontami użytkowników w katalogu:

1. W portalu Azure Dodaj użytkownika do usługi Active Directory: 

   a. Zaloguj się do [Centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
    
   b. Wybierz **usługi Azure Active Directory** , a następnie **użytkowników i grup**.
    
   c. Na **użytkowników i grup**, wybierz pozycję **wszyscy użytkownicy**, a następnie wybierz **nowego użytkownika**.
   
      **Użytkownika** zostanie otwarte okienko.
      
      ![W okienku "Użytkownika"](./media/add-user.png)
    
   d. Wprowadź szczegóły użytkownika, takich jak **nazwa** i **nazwy użytkownika**. Część nazwy domeny nazwa użytkownika musi być początkowej domyślnej domeny nazwa "[nazwa domeny].onmicrosoft.com" lub zweryfikowane, niefederacyjnych [niestandardowej nazwy domeny](../../active-directory/add-custom-domain.md) takich jak "contoso.com".
    
   e. Skopiuj lub w przeciwnym razie należy pamiętać wygenerowane hasło, dzięki czemu można udostępnić go użytkownikowi po zakończeniu tego procesu.
    
   f. Opcjonalnie można otwierać i Wypełnij informacje w **profilu**, **grup**, lub **roli katalogu** dla użytkownika. 
    
   g. Na **użytkownika**, wybierz pozycję **Utwórz**.
    
   h. Bezpiecznie dystrybucji wygenerowane hasło dla nowego użytkownika, dzięki czemu użytkownicy mogą się logować.

2. Utwórz wystąpienie usługi Azure AD DS. Postępuj zgodnie z instrukcjami w artykule [włączyć usługi Azure Active Directory Domain Services przy użyciu portalu Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (w zadaniach 1-5). Należy zaktualizować istniejące hasła użytkownika w usłudze Active Directory, tak aby hasła w usłudze Azure AD DS jest synchronizowany. Jest również należy dodać do usługi Azure AD DS, DNS, zgodnie z opisem w zadaniu 4 tego artykułu. 

3. Należy utworzyć oddzielne podsieci DSVM w sieci wirtualnej utworzone w zadaniu 2 w poprzednim kroku.
4. Utwórz co najmniej jedno wystąpienie maszyny Wirtualnej nauki danych w podsieci DSVM. 
5. Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) dodać DSVM do usługi Active Directory. 
6. Instalowanie udziału plików platformy Azure do hostowania katalogu głównej lub notes umożliwiające instalowanie obszaru roboczego na dowolnym komputerze. (Ścisłej uprawnień na poziomie plików, należy należy NFS z systemem w przynajmniej jednej maszyny wirtualnej.)

   a. [Tworzenie udziału plików Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Zainstaluj ją na DSVM systemu Linux. Po wybraniu **Connect** przycisk dla udziału plików platformy Azure na koncie magazynu w portalu Azure polecenie do uruchomienia w Bash, pojawi się powłoki na DSVM systemu Linux. Polecenie wygląda następująco:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
7. Przykładowa zainstalowany z udziału plików Azure w /data/workspace, np. Teraz można utworzyć katalogów dla poszczególnych użytkowników w udziale: /data/workspace/user1, /data/workspace/user2 i tak dalej. Utwórz `notebooks` katalogu, w obszarze roboczym każdego użytkownika. 
8. Tworzenie łącza symbolicznego `notebooks` w `$HOME/userx/notebooks/remote`.   

Teraz Poproś użytkowników w wystąpieniu usługi Active Directory hostowana na platformie Azure. Przy użyciu poświadczeń usługi Active Directory, użytkownicy mogą zalogować się do żadnych DSVM (SSH lub JupyterHub), który jest przyłączony do usługi Azure AD DS. Ponieważ obszar roboczy użytkownika znajduje się w udziale plików Azure, użytkownicy mają dostęp do ich notesów i inne zadania z dowolnym DSVM, gdy używają JupyterHub. 

Dla Skalowanie automatyczne można ustawić tworzenie puli maszyn wirtualnych, które należą do domeny w taki sposób, jak i z udostępnionego dysku, zainstalowane skali maszyny wirtualnej. Użytkownicy mogą zalogować się do dowolnej maszyny dostępne w zestawie skalowania maszyn wirtualnych i mieć dostęp do udostępnionego dysku, na którym są zapisywane ich notesów. 

## <a name="next-steps"></a>Kolejne kroki

* [Bezpiecznie przechowywać poświadczenia dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)



