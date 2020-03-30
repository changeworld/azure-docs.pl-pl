---
title: Zarządzanie usługami Analizy Platformy Azure | Dokumenty firmy Microsoft
description: W tym artykule opisano narzędzia używane do zarządzania zadaniami administracyjnymi i administracyjnymi dla serwera usług Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28d7b2955c84833841760e441cd2919181e22bc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73572798"
---
# <a name="manage-analysis-services"></a>Zarządzanie usługami Analysis Services
Po utworzeniu serwera usług Analysis Services na platformie Azure mogą istnieć pewne zadania administracyjne i administracyjne, które należy wykonać od razu lub kiedyś w dół drogi. Na przykład uruchom przetwarzanie danych odświeżania, kontroluj, kto może uzyskać dostęp do modeli na serwerze, lub monitoruj kondycję serwera. Niektóre zadania zarządzania można wykonywać tylko w witrynie Azure portal, inne w programie SQL Server Management Studio (SSMS), a niektóre zadania można wykonać w obu.

## <a name="azure-portal"></a>Portal Azure
[Witryna Azure portal](https://portal.azure.com/) to miejsce, w którym można tworzyć i usuwać serwery, monitorować zasoby serwera, zmieniać rozmiar i zarządzać tym, kto ma dostęp do serwerów.  Jeśli masz jakieś problemy, możesz również przesłać prośbę o pomoc techniczną.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Łączenie się z serwerem na platformie Azure jest jak łączenie się z wystąpieniem serwera we własnej organizacji. Z usługi SSMS można wykonywać wiele tych samych zadań, takich jak dane przetwarzania lub tworzyć skrypt przetwarzania, zarządzać rolami i używać programu PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Pobieranie i instalowanie programu SSMS
Aby uzyskać wszystkie najnowsze funkcje i najpłynniejsze środowisko podczas łączenia się z serwerem usług Azure Analysis Services, upewnij się, że używasz najnowszej wersji usługi SSMS. 

[Pobierz program SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Aby połączyć się z pomocą SSMS
 Korzystając z usługi SSMS, przed nawiązaniem połączenia z serwerem po raz pierwszy upewnij się, że nazwa użytkownika znajduje się w grupie Administratorzy usług Analysis Services. Aby dowiedzieć się więcej, zobacz [Administratorzy serwera i użytkownicy bazy danych](#server-administrators-and-database-users) w dalszej części tego artykułu.

1. Przed nawiązaniem połączenia należy uzyskać nazwę serwera. Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
    ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. W programie SSMS wybierz pozycję **Eksplorator obiektów**, a następnie kliknij pozycję **Połącz** > **Analysis Services**.
3. W oknie dialogowym **Łączenie z serwerem** wklej nazwę serwera, a następnie w oknie **Uwierzytelnianie**wybierz jeden z następujących typów uwierzytelniania:   
    > [!NOTE]
    > Typ uwierzytelniania, **Active Directory — uniwersalny z obsługą usługi MFA**.

    > [!NOTE]
    > Jeśli zalogujesz się za pomocą konta Microsoft, live id, Yahoo, Gmail itp., pozostaw pole hasła puste. Po kliknięciu przycisku Połącz zostanie wyświetlony monit o podanie hasła.

    **Uwierzytelnianie systemu Windows** do używania poświadczeń domeny systemu Windows\nazwa użytkownika i hasło.

    **Uwierzytelnianie hasłem usługi Active Directory** do używania konta organizacji. Na przykład podczas łączenia się z komputera przyłączonego do domeny.

    **Active Directory — uniwersalne z obsługą usługi MFA** do [używania uwierzytelniania nieinterakcyjnego lub wieloskładnikowego](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Nawiązywanie połączenia w programie SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administratorzy serwerów i użytkownicy baz danych
W usłudze Azure Analysis Services istnieją dwa typy użytkowników, administratorzy serwerów i użytkownicy bazy danych. Oba typy użytkowników muszą znajdować się w usłudze Azure Active Directory i muszą być określone przez organizacyjny adres e-mail lub upn. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Rozwiązywanie problemów z połączeniem
Podczas łączenia się przy użyciu usługi SSMS, jeśli napotkasz problemy, może być konieczne wyczyszczenie pamięci podręcznej logowania. Nic nie jest buforowane na płytę. Aby wyczyścić pamięć podręczną, zamknij i uruchom ponownie proces połączenia. 

## <a name="next-steps"></a>Następne kroki
Jeśli model tabelaryczny nie został jeszcze wdrożony na nowym serwerze, teraz jest dobry moment. Aby dowiedzieć się więcej, zobacz artykuł [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Wdrażanie w usługach Azure Analysis Services).

Jeśli model został wdrożony na serwerze, możesz połączyć się z nim za pomocą klienta lub przeglądarki. Aby dowiedzieć się więcej, zobacz [Odbieranie danych z serwera usług Azure Analysis Services](analysis-services-connect.md).

