---
title: Skonfiguruj wspólną tożsamość dla maszyny wirtualnej analizy danych — Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć typowych kont użytkowników, których można użyć między wieloma maszynami wirtualnymi analizy danych. Azure Active Directory lub lokalnej usługi Active Directory służy do uwierzytelniania kont użytkowników do maszyny wirtualnej do nauki o danych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0146ee6ee37c2eb9e98d831b54df2218d7de5b62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502391"
---
# <a name="set-up-a-common-identity-on-the-data-science-virtual-machine"></a>Skonfiguruj wspólną tożsamość na maszynie wirtualnej do nauki o danych

Na maszynie wirtualnej platformy Azure (VM), łącznie z maszyny wirtualnej do nauki o danych (DSVM) należy utworzyć lokalne konta użytkowników podczas aprowizowania maszyny Wirtualnej. Przy użyciu tych poświadczeń użytkowników uwierzytelniania do maszyny Wirtualnej. W przypadku wielu maszyn wirtualnych, które muszą uzyskać dostęp do tego podejścia może szybko uzyskać kłopotliwe w przypadku zarządzania poświadczeniami. Typowych kont użytkowników i zarządzania za pośrednictwem dostawcy tożsamości oparte na standardach umożliwiają korzystanie z jednego zestawu poświadczeń dostępu do wielu zasobów na platformie Azure, w tym wiele maszyny. 

Usługi Active Directory jest to dostawca tożsamości popularnych i jest obsługiwane na platformie Azure jako usługi i dla lokalnego. Można użyć usługi Azure Active Directory (Azure AD) lub lokalnej usługi Active Directory do uwierzytelniania użytkowników w autonomicznej maszyny wirtualnej DSVM lub klastra maszyny w zestawie skalowania maszyn wirtualnych platformy Azure. Można to zrobić, dołączając do wystąpienia maszyny wirtualnej DSVM do domeny usługi Active Directory. 

Jeśli masz już usługi Active Directory do zarządzania tożsamościami, można użyć go jako wspólnej dostawcy tożsamości. Jeśli nie masz usługi Active Directory, możesz uruchomić zarządzanego wystąpienia usługi Active Directory na platformie Azure za pośrednictwem usługi o nazwie [usługi Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (Azure AD DS). 

W dokumentacji dotyczącej [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) zapewnia szczegółowe [instrukcje zarządzania](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), łącznie z usługi Azure AD do katalogu lokalnego, jeśli nie masz. 

W tym artykule opisano kroki, aby skonfigurować w pełni zarządzana usługa domeny usługi Active Directory na platformie Azure przy użyciu usługi Azure AD DS. Następnie możesz dołączyć swoje maszyny do domeny zarządzanej usługi Active Directory, aby umożliwić użytkownikom dostęp do puli maszyny (i innych zasobów platformy Azure), za pomocą wspólnego konta użytkowników i poświadczeń. 

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurowanie domeny usługi Active Directory w pełni zarządzane na platformie Azure

Usługi Azure AD DS upraszcza zarządzanie tożsamości, podając w pełni zarządzana usługa na platformie Azure. W tej domenie usługi Active Directory Zarządzanie użytkownikami i grupami. Czynności do skonfigurowania hostowanej na platformie Azure Active Directory konta domeny i użytkownika w katalogu są następujące:

1. W witrynie Azure portal Dodaj użytkownika do usługi Active Directory: 

   a. Zaloguj się w [centrum administracyjnym usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
    
   b. Wybierz pozycję **Azure Active Directory**, a następnie **Użytkownicy i grupy**.
    
   c. W obszarze **Użytkownicy i grupy** wybierz pozycję **Wszyscy użytkownicy**, a następnie **Nowy użytkownik**.
   
      **Użytkownika** zostanie otwarte okienko.
      
      ![W okienku "User"](./media/add-user.png)
    
   d. Wprowadź dane użytkownika, na przykład **Nazwisko** i **Nazwę użytkownika**. Część nazwy domeny nazwa użytkownika musi mieć wartość wstępnej domyślnej domeny nazwa "[nazwa domeny].onmicrosoft.com" lub zweryfikowaną, niefederacyjną [niestandardowej nazwy domeny](../../active-directory/add-custom-domain.md) takich jak "contoso.com".
    
   e. Skopiuj lub w inny sposób zanotuj wygenerowane hasło użytkownika, aby przekazać je użytkownikowi po ukończeniu tego procesu.
    
   f. Opcjonalnie możesz otworzyć sekcje **Profil**, **Grupy** lub **Rola katalogu** dla użytkownika i uzupełnić w nich informacje. 
    
   g. W obszarze **Użytkownik** wybierz pozycję **Utwórz**.
    
   h. Bezpiecznie przekaż wygenerowane hasło nowemu użytkownikowi, aby mógł się zalogować.

1. Utwórz wystąpienie usługi Azure AD DS. Postępuj zgodnie z instrukcjami w artykule [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (w zadaniach 1-5). Należy zaktualizować istniejące hasła użytkownika w usłudze Active Directory jest synchronizowana z hasła w usłudze Azure AD DS. Należy również dodać DNS do usługi Azure AD DS, zgodnie z opisem w zadaniu 4 tego artykułu. 

1. Utwórz oddzielną podsieć maszyny wirtualnej DSVM w sieci wirtualnej utworzona w zadaniu 2 w poprzednim kroku.
1. Utwórz co najmniej jedno wystąpienie maszyny Wirtualnej do nauki o danych w podsieci maszyny wirtualnej DSVM. 
1. Postępuj zgodnie z [instrukcje](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) można dodać maszyny wirtualnej DSVM do usługi Active Directory. 
1. Instalowanie udziału plików platformy Azure do hostowania katalogu głównej lub Notes, aby umożliwić zainstalowanie obszaru roboczego na dowolnym komputerze. (Ścisłą uprawnień na poziomie plików, należy należy uruchomione na co najmniej jedna maszyna wirtualna systemu plików NFS.)

   a. [Tworzenie udziału plików platformy Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   b. Zainstaluj go na DSVM systemu Linux. Po wybraniu **Connect** przycisku dla usługi Azure Files udostępniania w ramach konta magazynu w witrynie Azure portal, uruchomienie polecenia powłoki bash powłoka w systemie Linux maszyny wirtualnej DSVM pojawia się. Polecenie wygląda następująco:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Załóżmy zainstalowanego udziału plików platformy Azure w /data/workspace, na przykład. Teraz tworzyć katalogi dla poszczególnych użytkowników w udziale: /data/workspace/user1, /data/workspace/user2 i tak dalej. Utwórz `notebooks` katalogu, w obszarze roboczym każdego użytkownika. 
1. Tworzenie łączy symbolicznych dla `notebooks` w `$HOME/userx/notebooks/remote`.   

Teraz Poproś użytkowników w Twoim wystąpieniu usługi Active Directory hostowanych na platformie Azure. Za pomocą poświadczeń usługi Active Directory, użytkownicy mogą zalogować się do żadnych DSVM (SSH lub JupyterHub), który jest przyłączony do usługi Azure AD DS. Ponieważ obszaru roboczego użytkownika znajduje się w udziale usługi Azure Files, użytkownicy mają dostęp do swoich notesów i innych zadań z dowolnej maszyny wirtualnej DSVM podczas korzystania z nich JupyterHub. 

Dla skalowania automatycznego możesz użyć zestawu do tworzenia puli maszyn wirtualnych, które należą do domeny w taki sposób, jak i z udostępnionym dyskiem zainstalowany skalowania maszyny wirtualnej. Użytkownicy mogą zalogować się do dostępnych maszyn w zestawie skalowania maszyn wirtualnych i mieć dostęp do udostępnionego dysku, na którym są zapisywane notesy. 

## <a name="next-steps"></a>Kolejne kroki

* [Bezpieczne przechowywanie poświadczeń w celu dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)



