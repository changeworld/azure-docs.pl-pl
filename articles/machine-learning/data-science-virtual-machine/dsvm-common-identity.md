---
title: Skonfiguruj wspólną tożsamość
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak utworzyć typowych kont użytkowników, których można użyć między wieloma maszynami wirtualnymi analizy danych. Azure Active Directory lub lokalnej usługi Active Directory służy do uwierzytelniania kont użytkowników do maszyny wirtualnej do nauki o danych.
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208149"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Skonfiguruj wspólną tożsamość na Data Science Virtual Machine

Na Microsoft Azure maszynę wirtualną (VM), w tym Data Science Virtual Machine (DSVM), tworzysz konta użytkowników lokalnych podczas aprowizacji maszyny wirtualnej. Przy użyciu tych poświadczeń użytkowników uwierzytelniania do maszyny Wirtualnej. Jeśli masz wiele maszyn wirtualnych, do których użytkownicy potrzebują dostępu, zarządzanie poświadczeniami może być bardzo uciążliwe. Doskonałe rozwiązanie polega na wdrożeniu wspólnych kont użytkowników i zarządzania za pośrednictwem dostawcy tożsamości opartego na standardach. Korzystając z tej metody, można użyć jednego zestawu poświadczeń w celu uzyskania dostępu do wielu zasobów na platformie Azure, w tym wielu DSVMs.

Active Directory to popularny dostawca tożsamości i jest obsługiwany na platformie Azure jako usługa w chmurze i jako katalog lokalny. Można użyć usługi Azure Active Directory (Azure AD) lub lokalnej usługi Active Directory do uwierzytelniania użytkowników w autonomicznej maszyny wirtualnej DSVM lub klastra maszyny w zestawie skalowania maszyn wirtualnych platformy Azure. Można to zrobić, dołączając do wystąpienia maszyny wirtualnej DSVM do domeny usługi Active Directory.

Jeśli masz już Active Directory, możesz użyć jej jako wspólnego dostawcy tożsamości. Jeśli nie masz Active Directory, możesz uruchomić zarządzane wystąpienie Active Directory na platformie Azure za pomocą [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/) (AD DS platformy Azure).

Dokumentacja [usługi Azure AD](https://docs.microsoft.com/azure/active-directory/) zawiera szczegółowe [instrukcje dotyczące zarządzania](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution), w tym wskazówki dotyczące łączenia usługi Azure AD z katalogiem lokalnym, jeśli go masz.

W tym artykule opisano sposób konfigurowania w pełni zarządzanej usługi domeny Active Directory na platformie Azure przy użyciu usługi Azure AD DS. Następnie można przyłączyć się do DSVMs do domeny zarządzanej Active Directory. Takie podejście umożliwia użytkownikom dostęp do puli DSVMs (i innych zasobów platformy Azure) za pomocą wspólnego konta użytkownika i poświadczeń.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurowanie domeny usługi Active Directory w pełni zarządzane na platformie Azure

Usługi Azure AD DS upraszcza zarządzanie tożsamości, podając w pełni zarządzana usługa na platformie Azure. W tej domenie usługi Active Directory Zarządzanie użytkownikami i grupami. Aby skonfigurować domenę Active Directory i konta użytkowników hostowane na platformie Azure, wykonaj następujące kroki:

1. W witrynie Azure portal Dodaj użytkownika do usługi Active Directory: 

   1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
    
   1. Wybierz pozycję **Azure Active Directory**, a następnie **Użytkownicy i grupy**.
    
   1. W obszarze **Użytkownicy i grupy**wybierz pozycję **Wszyscy użytkownicy**, a następnie wybierz pozycję **nowy użytkownik**.
   
           The **User** pane opens:
      
      ![W okienku "User"](./media/add-user.png)
    
   1. Wprowadź dane użytkownika, na przykład **Nazwisko** i **Nazwę użytkownika**. Część nazwy domeny nazwa użytkownika musi mieć wartość wstępnej domyślnej domeny nazwa "[nazwa domeny].onmicrosoft.com" lub zweryfikowaną, niefederacyjną [niestandardowej nazwy domeny](../../active-directory/add-custom-domain.md) takich jak "contoso.com".
    
   1. Skopiuj lub w inny sposób zanotuj wygenerowane hasło użytkownika, aby przekazać je użytkownikowi po ukończeniu tego procesu.
    
   1. Opcjonalnie możesz otworzyć sekcje **Profil**, **Grupy** lub **Rola katalogu** dla użytkownika i uzupełnić w nich informacje. 
    
   1. W obszarze **użytkownik**wybierz pozycję **Utwórz**.
    
   1. Bezpiecznie Rozpowszechnij wygenerowane hasło do nowego użytkownika, aby mogli się zalogować.

1. Utwórz wystąpienie usługi Azure AD DS. Postępuj zgodnie z instrukcjami w artykule [włączanie Azure Active Directory Domain Services przy użyciu Azure Portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (sekcja "Tworzenie wystąpienia i Konfigurowanie ustawień podstawowych"). Należy zaktualizować istniejące hasła użytkownika w usłudze Active Directory jest synchronizowana z hasła w usłudze Azure AD DS. Ważne jest również, aby dodać serwer DNS do usługi Azure AD DS, zgodnie z opisem w sekcji "Wypełnij pola w oknie podstawy Azure Portal, aby utworzyć wystąpienie usługi Azure AD DS".

1. Utwórz oddzielną podsieć DSVM w sieci wirtualnej utworzonej w sekcji "Tworzenie i Konfigurowanie sieci wirtualnej" w poprzednim kroku.
1. Utwórz co najmniej jedno wystąpienie DSVM w podsieci DSVM.
1. Postępuj zgodnie z [instrukcjami](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) , aby dodać DSVM do Active Directory. 
1. Zainstaluj udział Azure Files w celu hostowania katalogu macierzystego lub notesu, aby można było zainstalować ten obszar roboczy na dowolnym komputerze. (Jeśli potrzebujesz ścisłych uprawnień na poziomie pliku, musisz mieć sieciowy system plików [NFS] uruchomiony na co najmniej jednej maszyny wirtualnej).

   1. [Tworzenie udziału plików platformy Azure](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Zainstaluj ten udział w systemie Linux DSVM. Po wybraniu opcji **Połącz** dla udziału Azure Files na koncie magazynu w Azure Portal zostanie wyświetlony polecenie Uruchom w powłoce bash na DSVM systemu Linux. Polecenie wygląda następująco:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Załóżmy na przykład, że zainstalowano udział Azure Files w/Data/Workspace. Teraz utwórz katalogi dla każdego z użytkowników w udziale:/Data/Workspace/user1,/Data/Workspace/User2 i tak dalej. Utwórz `notebooks` katalogu, w obszarze roboczym każdego użytkownika. 
1. Tworzenie łączy symbolicznych dla `notebooks` w `$HOME/userx/notebooks/remote`.   

Teraz użytkownicy znajdują się w wystąpieniu Active Directory hostowanym na platformie Azure. Przy użyciu poświadczeń Active Directory użytkownicy mogą logować się do dowolnego DSVM (SSH lub JupyterHub) dołączonego do usługi Azure AD DS. Ponieważ obszaru roboczego użytkownika znajduje się w udziale usługi Azure Files, użytkownicy mają dostęp do swoich notesów i innych zadań z dowolnej maszyny wirtualnej DSVM podczas korzystania z nich JupyterHub.

Dla skalowania automatycznego możesz użyć zestawu do tworzenia puli maszyn wirtualnych, które należą do domeny w taki sposób, jak i z udostępnionym dyskiem zainstalowany skalowania maszyny wirtualnej. Użytkownicy mogą logować się na dowolnym komputerze z zestawu skalowania maszyn wirtualnych i mieć dostęp do udostępnionego dysku, na którym są zapisane ich notesy. 

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne przechowywanie poświadczeń w celu dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)



