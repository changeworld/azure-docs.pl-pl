---
title: Zarządzanie usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać serwerem usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61065199"
---
# <a name="manage-analysis-services"></a>Zarządzanie usług Analysis Services
Po utworzeniu serwera usług Analysis Services na platformie Azure, może to być niektóre administrowanie i zarządzanie zadania, które należy wykonać w dół po drodze następnie od razu lub później. Na przykład uruchomić przetwarzania odświeżania danych, kontrolowania, kto może uzyskiwać dostęp do modeli na serwerze lub monitorowanie kondycji serwera. Niektóre zadania zarządzania, można wykonać tylko w witrynie Azure portal, inne osoby w SQL Server Management Studio (SSMS), a niektóre zadania może odbywać się w jednym.

## <a name="azure-portal"></a>Azure Portal
[Witryna Azure portal](https://portal.azure.com/) jest, gdzie możesz można utworzyć i Usuń serwery, Monitoruj zasoby serwera, zmienianie rozmiaru i zarządzanie, kto ma dostęp do serwerów.  Jeśli występują problemy, można wysyłać również żądanie pomocy technicznej.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Nawiązywanie połączenia z serwerem na platformie Azure jest podobne do nawiązywania połączenia z wystąpieniem serwera w danej organizacji. W programie SSMS można wykonać wiele tych samych zadań, takich jak przetwarzanie danych lub utworzyć skrypt przetwarzania, Zarządzanie rolami i przy użyciu programu PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Pobieranie i instalowanie programu SSMS
Aby uzyskać najnowsze funkcje i daje płynne doświadczenia podczas nawiązywania połączenia z serwerem usług Azure Analysis Services, upewnij się, że używasz najnowszej wersji programu SSMS. 

[Pobieranie programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Aby połączyć za pomocą programu SSMS
 Korzystając z programu SSMS, przed nawiązaniem połączenia z serwerem po raz pierwszy, upewnij się, że Twoja nazwa użytkownika jest uwzględniona w grupie Administratorzy usług Analysis Services. Aby dowiedzieć się więcej, zobacz [bazy danych użytkowników i administratorów serwera](#server-administrators-and-database-users) w dalszej części tego artykułu.

1. Przed nawiązaniem połączenia, należy uzyskać nazwę serwera. Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
    ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. W programie SSMS wybierz pozycję **Eksplorator obiektów**, a następnie kliknij pozycję **Połącz** > **Analysis Services**.
3. W **Połącz z serwerem** okno dialogowe, Wklej nazwę serwera, a następnie w **uwierzytelniania**, wybierz jedną z następujących typów uwierzytelniania:   
    > [!NOTE]
    > Typ uwierzytelniania, **usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**, jest zalecane.

    > [!NOTE]
    > Jeśli zalogujesz Account Microsoft Live ID, Yahoo, Gmail, itp., pozostaw puste pole hasła. Zostanie wyświetlony monit o podanie hasła po kliknięciu przycisku Połącz.

    **Uwierzytelnianie Windows** poświadczenia Windows domena\nazwa_użytkownika i hasło.

    **Uwierzytelnianie hasłem usługi Active Directory** do użycia z poziomu konta organizacji. Na przykład podczas nawiązywania połączenia z innej domeny przyłączone do komputera.

    **Usługi Active Directory — uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego** używać [uwierzytelnianie nieinterakcyjne lub usługi Multi-Factor Authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Nawiązywanie połączenia w programie SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administratorzy serwera i bazy danych użytkowników
W usługach Azure Analysis Services istnieją dwa typy użytkowników, administratorów i użytkowników bazy danych. Użytkownicy obu typów musi znajdować się w usłudze Azure Active Directory i musi być określona za pomocą adresu e-mail organizacji lub nazwy UPN. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Rozwiązywanie problemów z połączeniem
Podczas nawiązywania połączenia przy użyciu programu SSMS, jeśli napotkasz problemy, może być konieczne wyczyszczenie pamięci podręcznej logowania. Nic nie jest buforowana na dysku. Aby wyczyścić pamięć podręczną, zamknij i uruchom ponownie proces connect. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie zostały już wdrożone modelu tabelarycznego, do nowego serwera, teraz jest dobry moment na to. Aby dowiedzieć się więcej, zobacz artykuł [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Wdrażanie w usługach Azure Analysis Services).

Jeśli wdrożono modelu do serwera, możesz nawiązać z nim za pomocą klienta lub przeglądarki. Aby dowiedzieć się więcej, zobacz [pobieranie danych z serwera usług Azure Analysis Services](analysis-services-connect.md).

