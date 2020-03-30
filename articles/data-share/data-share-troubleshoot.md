---
title: Rozwiązywanie problemów z usługą Azure Data Share
description: Dowiedz się, jak rozwiązywać problemy z zaproszeniami i błędami podczas tworzenia lub odbierania udziałów danych za pomocą usługi Azure Data Share.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.openlocfilehash: 901f2b56bc045dc9a9837dd18b2e6ce7169aa3b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76964230"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Rozwiązywanie typowych problemów w udziale danych platformy Azure 

W tym artykule pokazano, jak rozwiązać typowe problemy dotyczące udostępniania danych platformy Azure. 

## <a name="azure-data-share-invitations"></a>Zaproszenia do udostępniania danych platformy Azure 

W niektórych przypadkach, gdy nowy użytkownik kliknie przycisk **Zaakceptuj zaproszenie** z wysłanego zaproszenia e-mail, może zostać wyświetlony z pustą listą zaproszeń. 

![Brak zaproszeń](media/no-invites.png)

Może to być spowodowane następującymi przyczynami:

* **Usługa Azure Data Share nie jest zarejestrowana jako dostawca zasobów dowolnej subskrypcji platformy Azure w dzierżawie platformy Azure.** Ten problem wystąpi, jeśli w dzierżawie platformy Azure nie ma żadnych zasobów udziału danych. Podczas tworzenia zasobu usługi Azure Data Share automatycznie rejestruje dostawcę zasobów w subskrypcji platformy Azure. Można również ręcznie zarejestrować usługę udostępniania danych, wykonując następujące kroki. Aby wykonać te kroki, musisz mieć rolę współautora platformy Azure.

    1. W witrynie Azure portal przejdź do **subskrypcji**
    1. Wybierz subskrypcję, której chcesz użyć do utworzenia zasobu usługi Azure Data Share
    1. Kliknij dostawców **zasobów**
    1. Wyszukaj **witrynę Microsoft.DataShare**
    1. Kliknij **pozycję Zarejestruj się** 

    Aby wykonać te kroki, musisz mieć [rolę RBAC współautora platformy Azure.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 

* **Zaproszenie jest wysyłane do aliasu e-mail zamiast adresu e-mail logowania platformy Azure.** Jeśli użytkownik zarejestrował usługę Azure Data Share lub utworzył już zasób udostępniania danych w dzierżawie platformy Azure, ale nadal nie może wyświetlić zaproszenia, być może dlatego, że dostawca wprowadził alias e-mail jako adresata zamiast adresu e-mail logowania platformy Azure. Skontaktuj się z dostawcą danych i upewnij się, że wysłali zaproszenie na twój adres e-mail logowania platformy Azure, a nie alias e-mail.

* **Zaproszenie zostało już przyjęte.** Łącze w wiadomości e-mail prowadzi do strony Zaproszenie do udostępniania danych w witrynie Azure Portal, która zawiera tylko oczekujące zaproszenia. Jeśli zaproszenie zostało już zaakceptowane, nie będzie ono już wyświetlane na stronie Zaproszenie do udostępniania danych. Przejdź do zasobu udostępniania danych, który został użyty do zaakceptowania zaproszenia do wyświetlania odebranych udziałów i konfigurowania docelowego ustawienia klastra usługi Azure Data Explorer.

## <a name="error-when-creating-or-receiving-a-new-share"></a>Błąd podczas tworzenia lub odbierania nowego udziału

"Nie można dodać zestawów danych"

"Nie można mapować zestawów danych"

"Nie można udzielić zasobu udostępniania danych x dostęp do y"

"Nie masz odpowiednich uprawnień do x"

"Nie można dodać uprawnień do zapisu dla konta usługi Azure Data Share do co najmniej jednego z wybranych zasobów"

Jeśli otrzymasz którykolwiek z powyższych błędów podczas tworzenia nowego udziału lub mapowania zestawów danych, może to być spowodowane niewystarczającymi uprawnieniami do magazynu danych platformy Azure. Zobacz [Role i wymagania](concepts-roles-permissions.md) dotyczące wymaganych uprawnień. 

Musisz napisać uprawnienia do udostępniania lub odbierania danych z magazynu danych platformy Azure, który zazwyczaj istnieje w roli współautora. 

Jeśli jest to pierwszy raz, gdy udostępniasz lub odbierasz dane z magazynu danych platformy Azure, potrzebujesz również uprawnień *Microsoft.Authorization/role assignments/write,* które zazwyczaj istnieje w roli właściciela. Nawet jeśli utworzono zasób magazynu danych platformy Azure, nie automatycznie uczynić cię właścicielem zasobu. Dzięki odpowiednim uprawnieniom usługa Azure Data Share automatycznie udziela zasobu udziału danych dostępu do tożsamości zarządzanej do magazynu danych. Ten proces może potrwać kilka minut, aby wejść w życie. Jeśli wystąpi awaria z powodu tego opóźnienia, spróbuj ponownie w ciągu kilku minut.

Udostępnianie oparte na języku SQL wymaga dodatkowych uprawnień. Aby uzyskać szczegółowe informacje, zobacz Rozwiązywanie problemów z udostępnianiem opartym na języku SQL.

## <a name="troubleshooting-sql-based-sharing"></a>Rozwiązywanie problemów z udostępnianiem opartym na języku SQL

"Użytkownik x nie istnieje w bazie danych SQL"

Jeśli ten błąd zostanie wyświetlony podczas dodawania zestawu danych ze źródła opartego na języku SQL, może to być spowodowane tym, że użytkownik nie został utworzony dla tożsamości zarządzanej usługi Azure Data Share na serwerze SQL Server.  Aby rozwiązać ten problem, uruchom następujący skrypt:

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
Jeśli ten błąd zostanie wyświetlony podczas mapowania zestawu danych do obiektu docelowego opartego na języku SQL, może to być spowodowane tym, że użytkownik nie został utworzony dla tożsamości zarządzanej usługi Azure Data Share na serwerze SQL Server.  Aby rozwiązać ten problem, uruchom następujący skrypt:

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
Należy zauważyć, że *<share_acc_name>* jest nazwą zasobu udziału danych.      

Upewnij się, że zostały zgodne ze wszystkimi wymaganiami wstępnymi wymienionymi w [udostępnianie danych](share-your-data.md) i [akceptować i odbierać dane](subscribe-to-data-share.md) samouczek.

## <a name="snapshot-failed"></a>Migawka nie powiodła się
Migawka może zakończyć się niepowodzeniem z różnych powodów. Szczegółowy komunikat o błędzie można znaleźć, klikając godzinę rozpoczęcia migawki, a następnie stan każdego zestawu danych. 

Jeśli komunikat o błędzie jest związany z uprawnieniem, sprawdź, czy usługa Udostępniania danych ma wymagane uprawnienia. Zobacz [role i wymagania, aby](concepts-roles-permissions.md) uzyskać szczegółowe informacje. Jeśli jest to pierwszy raz, gdy robisz migawkę, może upłynąć kilka minut dla zasobów udziału danych, aby uzyskać dostęp do magazynu danych platformy Azure. Odczekaj kilka minut i spróbuj ponownie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak rozpocząć udostępnianie danych, przejdź do [samouczka udostępniania danych.](share-your-data.md) 

Aby dowiedzieć się, jak odbierać dane, kontynuuj [akceptowanie i odbieranie danych](subscribe-to-data-share.md) samouczek.

