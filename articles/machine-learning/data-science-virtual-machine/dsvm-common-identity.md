---
title: Konfigurowanie wspólnej tożsamości
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak utworzyć typowe konta użytkowników, które mogą być używane w wielu maszynach wirtualnych do nauki o danych. Za pomocą usługi Azure Active Directory lub lokalnej usługi Active Directory można uwierzytelniać użytkowników na maszynie wirtualnej do nauki o danych.
keywords: głębokie uczenie się, sztuczna inteligencja, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analityka geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 44f1f7ae3b290e1dbf01877f3881e1d95a238446
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70208149"
---
# <a name="set-up-a-common-identity-on-a-data-science-virtual-machine"></a>Konfigurowanie wspólnej tożsamości na maszynie wirtualnej do nauki o danych

Na maszynie wirtualnej platformy Microsoft Azure (VM), w tym maszyny wirtualnej do nauki o danych (DSVM), można utworzyć konta użytkowników lokalnych podczas inicjowania obsługi administracyjnej maszyny wirtualnej. Użytkownicy następnie uwierzytelnić do maszyny Wirtualnej przy użyciu tych poświadczeń. Jeśli masz wiele maszyn wirtualnych, które użytkownicy muszą uzyskać dostęp, zarządzanie poświadczeniami może być bardzo uciążliwe. Doskonałym rozwiązaniem jest wdrożenie wspólnych kont użytkowników i zarządzania za pośrednictwem dostawcy tożsamości opartego na standardach. Dzięki temu podejściu można użyć jednego zestawu poświadczeń, aby uzyskać dostęp do wielu zasobów na platformie Azure, w tym wielu dsvms.

Usługa Active Directory jest popularnym dostawcą tożsamości i jest obsługiwana na platformie Azure zarówno jako usługa w chmurze, jak i jako katalog lokalny. Za pomocą usługi Azure Active Directory (Azure AD) lub lokalnej usługi Active Directory można uwierzytelniać użytkowników w autonomicznym systemie DSVM lub klastrze maszyn DSVM w zestawie skalowania maszyny wirtualnej platformy Azure. Można to zrobić, łącząc wystąpienia DSVM do domeny usługi Active Directory.

Jeśli masz już usługi Active Directory, można jej używać jako wspólnego dostawcy tożsamości. Jeśli nie masz usługi Active Directory, możesz uruchomić zarządzane wystąpienie usługi Active Directory na platformie Azure za pośrednictwem [usług domenowych Active Directory azure](https://docs.microsoft.com/azure/active-directory-domain-services/) (Usługi Azure AD DS).

Dokumentacja usługi [Azure AD](https://docs.microsoft.com/azure/active-directory/) zawiera szczegółowe [instrukcje zarządzania,](https://docs.microsoft.com/azure/active-directory/choose-hybrid-identity-solution)w tym wskazówki dotyczące łączenia usługi Azure AD z katalogiem lokalnym, jeśli go masz.

W tym artykule opisano sposób konfigurowania w pełni zarządzanej usługi domeny usługi Active Directory na platformie Azure przy użyciu usług Azure AD DS. Następnie można dołączyć do nazw DSVM do zarządzanej domeny usługi Active Directory. Takie podejście umożliwia użytkownikom dostęp do puli dsvms (i innych zasobów platformy Azure) za pośrednictwem wspólnego konta użytkownika i poświadczeń.

## <a name="set-up-a-fully-managed-active-directory-domain-on-azure"></a>Konfigurowanie w pełni zarządzanej domeny usługi Active Directory na platformie Azure

Usługi Azure AD DS ułatwia zarządzanie tożsamościami, zapewniając w pełni zarządzaną usługę na platformie Azure. W tej domenie usługi Active Directory zarządzasz użytkownikami i grupami. Aby skonfigurować domenę usługi Active Directory hostowane na platformie Azure w katalogu, wykonaj następujące kroki:

1. W witrynie Azure portal dodaj użytkownika do usługi Active Directory: 

   1. Zaloguj się do [centrum administracyjnego usługi Azure Active Directory](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym dla katalogu.
    
   1. Wybierz pozycję **Azure Active Directory**, a następnie **Użytkownicy i grupy**.
    
   1. W **obszarze Użytkownicy i grupy**wybierz pozycję Wszyscy **użytkownicy**, a następnie wybierz pozycję **Nowy użytkownik**.
   
           The **User** pane opens:
      
      ![Okienko "Użytkownik"](./media/add-user.png)
    
   1. Wprowadź dane użytkownika, na przykład **Nazwisko** i **Nazwę użytkownika**. Część nazwy domeny nazwy użytkownika musi być początkową domyślną nazwą domeny "[nazwa domeny].onmicrosoft.com" lub zweryfikowaną, niefederowaną [nazwą domeny niestandardowej,](../../active-directory/add-custom-domain.md) taką jak "contoso.com".
    
   1. Skopiuj lub w inny sposób zanotuj wygenerowane hasło użytkownika, aby przekazać je użytkownikowi po ukończeniu tego procesu.
    
   1. Opcjonalnie możesz otworzyć sekcje **Profil**, **Grupy** lub **Rola katalogu** dla użytkownika i uzupełnić w nich informacje. 
    
   1. W obszarze **Użytkownik**wybierz pozycję **Utwórz**.
    
   1. Bezpiecznie rozpowszechniaj wygenerowane hasło do nowego użytkownika, aby mógł się zalogować.

1. Tworzenie wystąpienia usług Azure AD DS. Postępuj zgodnie z instrukcjami w [włącz usługę Azure Active Directory Domain Services za pomocą witryny Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started) (sekcja "Tworzenie wystąpienia i konfigurowanie podstawowych ustawień"). Ważne jest, aby zaktualizować istniejące hasła użytkowników w usłudze Active Directory, tak aby hasło w usługach Azure AD DS było synchronizowane. Ważne jest również, aby dodać usługę DNS do usług Azure AD DS, zgodnie z opisem w sekcji "Uzupełnij pola w oknie Podstawy witryny Azure portal, aby utworzyć wystąpienie usług Azure AD DS" w tej sekcji.

1. Utwórz oddzielną podsieć DSVM w sieci wirtualnej utworzoną w sekcji "Utwórz i skonfiguruj sieć wirtualną" poprzedniego kroku.
1. Utwórz co najmniej jedno wystąpienie DSVM w podsieci DSVM.
1. Postępuj zgodnie z [instrukcjami,](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-join-ubuntu-linux-vm ) aby dodać dsvm do usługi Active Directory. 
1. Zamontuj udział usługi Azure Files, aby hostować katalog domowy lub notes, aby można było zainstalować obszar roboczy na dowolnym komputerze. (Jeśli potrzebujesz ścisłych uprawnień na poziomie pliku, musisz mieć system plików sieciowych [NFS] uruchomiony na co najmniej jednej maszynie wirtualnej).

   1. [Utwórz udział usługi Azure Files](../../storage/files/storage-how-to-create-file-share.md).
    
   2.  Zamontuj ten udział na linuxowym systemie DSVM. Po **wybraniu connect** for the Azure Files udział na koncie magazynu w witrynie Azure portal, zostanie wyświetlene polecenie do uruchomienia w powłoki bash na linux DSVM. Polecenie wygląda następująco:
   
   ```
   sudo mount -t cifs //[STORAGEACCT].file.core.windows.net/workspace [Your mount point] -o vers=3.0,username=[STORAGEACCT],password=[Access Key or SAS],dir_mode=0777,file_mode=0777,sec=ntlmssp
   ```
1. Załóżmy na przykład, że zainstalowano udział usługi Azure Files w obszarze roboczym /data/. Teraz utwórz katalogi dla każdego z użytkowników w udziale: /data/workspace/user1, /data/workspace/user2 itd. Utwórz `notebooks` katalog w obszarze roboczym każdego użytkownika. 
1. Tworzenie dowiązań `notebooks` `$HOME/userx/notebooks/remote`symbolicznych w pliku .   

Użytkownicy w wystąpieniu usługi Active Directory są hostowani na platformie Azure. Za pomocą poświadczeń usługi Active Directory użytkownicy mogą logować się do dowolnego dsvm (SSH lub JupyterHub), który jest przyłączony do usług Azure AD DS. Ponieważ obszar roboczy użytkownika znajduje się w udziale usługi Azure Files, użytkownicy mają dostęp do swoich notesów i innej pracy z dowolnego dsvm, gdy są one przy użyciu Usługi JupyterHub.

W przypadku skalowania automatycznego można użyć zestawu skalowania maszyny wirtualnej, aby utworzyć pulę maszyn wirtualnych, które są połączone z domeną w ten sposób i z zainstalowanym dyskiem udostępnionym. Użytkownicy mogą logować się do dowolnej dostępnej maszyny w zestawie skalowania maszyny wirtualnej i mieć dostęp do dysku udostępnionego, na którym są zapisywane ich notesy. 

## <a name="next-steps"></a>Następne kroki

* [Bezpieczne przechowywanie poświadczeń w celu uzyskania dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)



