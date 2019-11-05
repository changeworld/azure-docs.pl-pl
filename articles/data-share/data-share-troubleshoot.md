---
title: Rozwiązywanie problemów z usługą Azure Data Share
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub otrzymywania udziałów danych za pomocą udziału danych platformy Azure.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 6ad612d56b25da9e092070198e321e7fca8ad96b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490563"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Rozwiązywanie typowych problemów z udziałem danych platformy Azure 

W tym artykule pokazano, jak rozwiązywać typowe problemy z udziałem danych platformy Azure. 

## <a name="azure-data-share-invitations"></a>Zaproszenia udziału danych platformy Azure 

W niektórych przypadkach, gdy nowy użytkownik kliknie opcję **Zaakceptuj zaproszenie** z zaproszenia e-mail, które zostało wysłane, może zostać wyświetlona pusta lista zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Powyższy błąd to znany problem z usługą i aktualnie trwa jej rozwiązywanie. Aby obejść ten krok, wykonaj poniższe czynności. 

1. W Azure Portal przejdź do **subskrypcji**
1. Wybierz subskrypcję używaną dla udziału danych platformy Azure
1. Kliknij pozycję **dostawcy zasobów**
1. Wyszukaj w usłudze Microsoft. datashare
1. Kliknij pozycję **zarejestruj** .

Aby wykonać te kroki, musisz mieć [rolę RBAC współautor platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) . 

Jeśli nadal nie możesz zobaczyć zaproszenia do udziału danych, skontaktuj się z dostawcą danych i upewnij się, że przesłały zaproszenie do adresu e-mail logowania platformy Azure, a *nie* aliasu e-mail. 

> [!IMPORTANT]
> Jeśli zaakceptujesz już zaproszenie do udziału danych platformy Azure i zakończysz działanie usługi przed rozpoczęciem konfigurowania magazynu, postępuj zgodnie z instrukcjami szczegółowymi w temacie [Konfigurowanie mapowania zestawu danych](how-to-configure-mapping.md) — Przewodnik po tym, aby dowiedzieć się, jak zakończyć konfigurowanie otrzymanego udziału danych i zacznij odbierać dane. 

## <a name="error-when-creating-or-receiving-a-new-data-share"></a>Błąd podczas tworzenia lub otrzymywania nowego udziału danych

"Błąd: operacja zwróciła nieprawidłowy kod stanu" nieprawidłowego żądania ""

"Błąd: AuthorizationFailed"

"Błąd: przypisanie roli do konta magazynu"

![Błąd uprawnień](media/error-write-privilege.png)

Jeśli podczas tworzenia nowego udziału danych lub odbierania nowego udziału danych pojawi się jeden z powyższych błędów, oznacza to brak wystarczających uprawnień do konta magazynu. Wymagane uprawnienie to *Microsoft. Authorization/przypisania ról/zapis*, które istnieje w roli właściciela magazynu lub można przypisać do roli niestandardowej. Nawet jeśli utworzysz konto usługi Storage, NIE sprawi to, że automatycznie staniesz się właścicielem konta magazynu. Wykonaj poniższe kroki, aby przyznać sobie rolę właściciela konta magazynu. Alternatywnie można utworzyć rolę niestandardową z tym uprawnieniem, które można dodać samodzielnie do programu.  

1. Przejdź do konta usługi Storage w witrynie Azure Portal
1. Wybieranie **kontroli dostępu (IAM)**
1. Kliknij przycisk **Dodaj**
1. Dodaj siebie jako właściciela.

## <a name="troubleshooting-sql-based-sharing"></a>Rozwiązywanie problemów z udostępnianiem opartym na języku SQL

"Błąd: zestaw danych x nie został dodany, ponieważ nie masz wymaganych uprawnień do udostępniania".

Jeśli ten błąd wystąpi podczas dodawania zestawu danych z źródła opartego na języku SQL, może to być spowodowane faktem, że nie utworzono użytkownika dla elementu MSI udziału danych platformy Azure na SQL Server.  Aby rozwiązać ten problem, uruchom następujący skrypt:

```sql
    create user <share_acct_name> from external provider;     
    exec sp_addrolemember db_owner, <share_acct_name>; 
```      
Należy pamiętać, że *< share_acc_name >* to nazwa konta udziału danych. Jeśli konto udziału danych nie zostało jeszcze utworzone, możesz wrócić do tego wymagania wstępnego później.         

Upewnij się, że zostały spełnione wszystkie wymagania wstępne wymienione w samouczku [udostępnianie danych](share-your-data.md) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zacząć udostępniać dane, przejdź do samouczka [udostępnianie danych](share-your-data.md) .

