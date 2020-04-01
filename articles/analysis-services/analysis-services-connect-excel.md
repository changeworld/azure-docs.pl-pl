---
title: Łączenie się z usługami Azure Analysis Services za pomocą programu Excel | Dokumenty firmy Microsoft
description: Dowiedz się, jak połączyć się z serwerem usług Azure Analysis Services przy użyciu programu Excel. Po nawiązaniu połączenia użytkownicy mogą tworzyć tabele przestawne w celu eksplorowania danych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b5c46c8b85af59efaf611300e2ab7129b3ef3cde
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411385"
---
# <a name="connect-with-excel"></a>Łączenie z programem Excel

Po utworzeniu serwera i wdrożeniu modelu tabelaryczne do niego klienci mogą łączyć się i rozpocząć eksplorowanie danych. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

Konto, za pomocą którego się logujesz, musi należeć do roli modelowej bazy danych z co najmniej uprawnieniami do odczytu. Aby dowiedzieć się więcej, zobacz [Authentication and user permissions (Uwierzytelnianie i uprawnienia użytkownika)](analysis-services-manage-users.md). 

## <a name="connect-in-excel"></a>Łączenie w programie Excel

Łączenie się z serwerem w programie Excel jest obsługiwane przy użyciu funkcji Pobierz dane w programie Excel 2016 i nowszych. Łączenie za pomocą Kreatora importowanych tabel w dodatku Power Pivot nie jest obsługiwane. 

1. W programie Excel na wstążce **Dane** kliknij pozycję **Pobierz dane** > zewnętrzne**z innych źródeł** > **z usług Analysis Services**.

2. W Kreatorze połączenia danych w **nazwie serwera**wprowadź nazwę serwera, w tym protokół i identyfikator URI. Na przykład asazure://westcentralus.asazure.windows.net/advworks. Następnie w obszarze **Poświadczenia logowania**wybierz pozycję **Użyj następującej nazwy użytkownika i hasła**, nancy@adventureworks.coma następnie wpisz na przykład nazwę użytkownika organizacji i hasło.

    > [!IMPORTANT]
    > Jeśli zalogujesz się za pomocą konta Microsoft, live id, Yahoo, Gmail itp., lub musisz zalogować się za pomocą uwierzytelniania wieloskładnikowego, pozostaw pole hasła puste. Po kliknięciu przycisku Dalej zostanie wyświetlony monit o podanie hasła. 

    ![Łączenie z logowania programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-logon.png)

3. W **obszarze Wybierz bazę danych i tabelę**wybierz bazę danych oraz model lub perspektywę, a następnie kliknij przycisk **Zakończ**.
   
    ![Łączenie z wybranego modelu programu Excel](./media/analysis-services-connect-excel/aas-connect-excel-select.png)


## <a name="see-also"></a>Zobacz też

[Biblioteki klientów](analysis-services-data-providers.md)   
[Zarządzanie serwerem](analysis-services-manage.md)     


