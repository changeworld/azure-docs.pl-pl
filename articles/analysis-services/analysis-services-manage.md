---
title: Zarządzanie usług Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać serwerem usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0cad50788eeb4a31d1e3f6f7e6c038ba2a8d4d9b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="manage-analysis-services"></a>Zarządzanie usług Analysis Services
Po utworzeniu serwerem usług Analysis Services na platformie Azure, może to być niektórych zadań administracji i zarządzania, które należy wykonać od razu lub jakimś występujących. Na przykład uruchom odświeżanie danych, kontrolowania, kto może uzyskać dostępu modeli na serwerze lub monitorowania kondycji serwera przetwarzania. Niektóre zadania zarządzania można wykonać tylko w portalu Azure, inne osoby w programu SQL Server Management Studio (SSMS), a niektóre zadania można to zrobić na dwa.

## <a name="azure-portal"></a>Azure Portal
[Azure portal](http://portal.azure.com/) pozwala można utworzyć i usuwać serwery, monitorowanie zasobów serwera, zmienianie rozmiaru, i zarządzać dostępem do serwerów.  Jeśli występują problemy, możesz również przekazywać żądania pomocy technicznej.

![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Łączenie z serwerem na platformie Azure działa tak, jak nawiązywanie połączenia z wystąpieniem serwera w organizacji. Z SSMS można wykonywanie wielu zadań, takich jak przetwarzania danych lub utworzyć skrypt przetwarzania, do zarządzania rolami i przy użyciu programu PowerShell.
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>Pobierz i zainstaluj narzędzia SSMS
Aby uzyskać najnowsze funkcje i daje płynne środowisko podczas nawiązywania połączenia z serwerem usług Analysis Services dla platformy Azure, upewnij się, że używasz najnowszej wersji programu SSMS. 

[Pobieranie programu SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).


### <a name="to-connect-with-ssms"></a>Aby połączyć się ze SSMS
 Korzystając z narzędzia SSMS, przed nawiązaniem połączenia z serwerem po raz pierwszy, upewnij się, że nazwa użytkownika jest uwzględniona w grupie Administratorzy usług analizy. Aby dowiedzieć się więcej, zobacz [administratorom serwerów](#server-administrators) dalszej części tego artykułu.

1. Przed nawiązaniem połączenia, należy uzyskać nazwę serwera. Skopiuj nazwę serwera z **portalu Azure** > serwer > **Omówienie** > **Nazwa serwera**.
   
    ![Pobieranie nazwy serwera z systemu Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. W programie SSMS > **Eksplorator obiektów**, kliknij przycisk **Connect** > **usług Analysis Services**.
3. W **Połącz z serwerem** okno dialogowe, Wklej w polu Nazwa serwera, a następnie w **uwierzytelniania**, wybierz jedną z następujących typów uwierzytelniania:   
    > [!NOTE]
    > Typ uwierzytelniania **usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego**, jest zalecane.

    > [!NOTE]
    > Jeśli zalogujesz się przy użyciu Account Microsoft Live ID, Yanoo, Gmail, itp., pole jest puste hasło. Zostanie wyświetlony monit o podanie hasła po kliknięciu przycisku Połącz.

    **Uwierzytelnianie systemu Windows** do korzystania z poświadczeń domena azwa_użytkownika i hasło systemu Windows.

    **Uwierzytelnianie hasłem usługi Active Directory** używanie konta organizacyjnego. Na przykład podczas nawiązywania połączenia ze spoza domeny przyłączone do komputera.

    **Usługi Active Directory - uniwersalnego z obsługą uwierzytelniania Wieloskładnikowego** do używania [nieinterakcyjnym lub usługi Multi-Factor authentication](../sql-database/sql-database-ssms-mfa-authentication.md). 
   
    ![Połącz w programie SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>Administratorzy serwera i bazy danych użytkowników
W usłudze Azure Analysis Services istnieją dwa typy użytkowników, administratorów serwera i bazy danych użytkowników. Użytkownicy obu typów musi znajdować się w usłudze Azure Active Directory i musi być określona za pomocą adresu e-mail organizacji lub nazwy UPN. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md).


## <a name="troubleshooting-connection-problems"></a>Rozwiązywanie problemów z połączeniem
Podczas nawiązywania połączenia przy użyciu narzędzia SSMS, jeśli wystąpią problemy, należy wyczyścić pamięć podręczną logowania. Nic nie są buforowane na dysku. Aby wyczyścić pamięć podręczną, zamknij i uruchom proces connect. 

## <a name="next-steps"></a>Kolejne kroki
Jeśli nie zostało już wdrożone modelu tabelarycznego, do nowego serwera, teraz jest odpowiedni moment. Aby dowiedzieć się więcej, zobacz artykuł [Deploy to Azure Analysis Services](analysis-services-deploy.md) (Wdrażanie w usługach Azure Analysis Services).

Po wdrożeniu modelu do serwera, możesz połączyć się z nim za pomocą klienta lub przeglądarki. Aby dowiedzieć się więcej, zobacz [Pobierz dane z serwera usług Azure Analysis Services](analysis-services-connect.md).

