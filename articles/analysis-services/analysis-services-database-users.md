---
title: Zarządzanie rolami bazy danych i użytkowników w usługach Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać ról bazy danych i użytkowników na serwerze usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 462625ce61f4538aa0769667648e07cc6307cbb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61023635"
---
# <a name="manage-database-roles-and-users"></a>Zarządzanie rolami bazy danych i użytkowników

Na poziomie bazy danych modelu wszyscy użytkownicy muszą należeć do roli. Role definiują użytkownicy z uprawnieniami określonego dla bazy danych modelu. Dowolny użytkownik lub grupa zabezpieczeń została dodana do roli musi mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji co serwer. 

Jak zdefiniować role różni się w zależności od używanego narzędzia, ale efekt jest taki sam.

Uprawnienia roli obejmują:
*  **Administrator** — użytkownicy mają pełne uprawnienia do bazy danych. Role bazy danych z uprawnieniami administratora różnią się od administratorów serwera.
*  **Proces** — użytkownicy mogą nawiązać połączenie i wykonywać operacje przetwarzania w bazie danych i analizowanie danych z bazy danych modelu.
*  **Odczyt** — użytkownicy mogą używać aplikacji klienckiej na łączenie się i analizowanie danych z bazy danych modelu.

Podczas tworzenia projektu modelu tabelarycznego, tworzenie ról i dodawanie użytkowników lub grup do tych ról za pomocą menedżera ról w programie SSDT. Zainstalowane na serwerze, za pomocą programu SSMS, [poleceń cmdlet programu PowerShell usługi analizy](/sql/analysis-services/powershell/analysis-services-powershell-reference), lub [języka skryptów modelu tabelarycznego](https://msdn.microsoft.com/library/mt614797.aspx) TMSL () do dodawania lub usuwania ról i elementów członkowskich użytkownika.

> [!NOTE]
> Grupy zabezpieczeń musi mieć `MailEnabled` właściwością `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Aby dodać lub zarządzania rolami i użytkowników w programie SSDT  
  
1.  W programie SSDT > **Eksploratorze modeli tabelarycznych**, kliknij prawym przyciskiem myszy **role**.  
  
2.  W **Menedżerze ról** kliknij przycisk **Nowa**.  
  
3.  Wpisz nazwę roli.  
  
     Domyślnie nazwa domyślna rola przyrostowo ponumerowane dla każdej nowej roli. Zalecane jest, że możesz wpisać nazwę, która jednoznacznie identyfikuje typ elementu członkowskiego, na przykład menedżerów finansowego lub specjaliści ds. zarządzania zasobami ludzkimi.  
  
4.  Wybierz jedną z następujących uprawnień:  
  
    |Uprawnienie|Opis|  
    |----------------|-----------------|  
    |**Brak**|Elementy członkowskie nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
    |**Odczyt**|Elementy członkowskie wykonywać zapytania na danych (oparte na filtrów wierszy), ale nie można zmodyfikować schematu modelu.|  
    |**Odczyt i przetwarzanie**|Członkowie mogą wysyłać zapytania na danych (na poziomie wiersza filtrami) i wykonywania operacji przetwarzania i przetwórz wszystko, ale nie można zmodyfikować schematu modelu.|  
    |**Proces**|Członkowie mogą uruchamiać operacje procesu i przetwórz wszystko. Nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
    |**Administrator**|Elementy członkowskie można zmodyfikować schemat modelu i wyszukiwać wszystkie dane.|   
  
5.  Jeśli rola tworzenia ma odczytu lub uprawnienia Odczyt i przetwarzanie, można dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij przycisk **filtry wierszy** , a następnie wybierz tabelę, a następnie kliknij **Filtr języka DAX** pola, a następnie wpisz formułę języka DAX.
  
6.  Kliknij przycisk **członków** > **Dodaj zewnętrzny**.  
  
8.  W **Dodawanie zewnętrznego elementu członkowskiego**, wprowadź użytkowników lub grup w Twojej dzierżawie usługi Azure AD przy użyciu adresu e-mail. Po kliknij przycisk OK i zamknij menedżera ról, ról i członkowie roli są wyświetlane w Eksploratorze modeli tabelarycznych. 
 
     ![Ról i użytkowników w Eksploratorze modeli tabelarycznych](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Wdrożenia na serwerze usług Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Aby dodać lub zarządzania rolami i użytkowników w programie SSMS

Do dodawania ról i użytkowników, aby wdrożony model bazy danych, możesz muszą być połączone z serwerem jako administrator serwera lub już w roli bazy danych z uprawnieniami administratora.

1. W obiekcie Exporer, kliknij prawym przyciskiem myszy **role** > **nową rolę**.

2. W **Utwórz rolę**, wprowadź nazwę roli i opis.

3. Wybierz uprawnienie.

   |Uprawnienie|Opis|  
   |----------------|-----------------|  
   |**Pełna kontrola (Administrator)**|Członkowie mogą modyfikować schematu modelu przetwarzania danych i wykonywać zapytania na wszystkich danych.| 
   |**Proces bazy danych**|Członkowie mogą uruchamiać operacje procesu i przetwórz wszystko. Nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
   |**Odczyt**|Elementy członkowskie wykonywać zapytania na danych (oparte na filtrów wierszy), ale nie można zmodyfikować schematu modelu.|  
  
4. Kliknij przycisk **członkostwa**, a następnie wprowadź użytkownika lub grupy w Twojej dzierżawie usługi Azure AD przy użyciu adresu e-mail.

     ![Dodawanie użytkownika](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Jeśli rola, którą tworzysz ma uprawnienie do odczytu, można dodać filtry wierszy przy użyciu formuły języka DAX. Kliknij przycisk **filtry wierszy**, wybierz tabelę, a następnie wpisz formułę języka DAX w **Filtr języka DAX** pola. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Dodawanie ról i użytkowników za pomocą skryptów TMSL

W oknie XMLA w programie SSMS lub przy użyciu programu PowerShell, można uruchomić skryptów TMSL. Użyj [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) polecenia i [role](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) obiektu.

**Przykładowy skrypt TMSL**

W tym przykładzie użytkownik zewnętrzny B2B i grupy są dodawane do roli analityk z uprawnieniami do odczytu dla bazy danych SalesBI. Zarówno użytkownika zewnętrznego, jak i grupa musi być w tej samej dzierżawie usługi Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Do dodawania ról i użytkowników przy użyciu programu PowerShell

[SqlServer](/sql/analysis-services/powershell/analysis-services-powershell-reference) modułu zawiera polecenia cmdlet do zarządzania bazy danych specyficznych dla zadań i ogólnego przeznaczenia polecenia cmdlet Invoke-ASCmd, które akceptuje zapytanie skryptów języka TMSL (Tabular Model) lub skrypt. Następujące polecenia cmdlet są używane do zarządzania rolami bazy danych i użytkowników.
  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Add-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Dodaj członka do roli bazy danych.| 
|[Remove-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Usuń członka z roli bazy danych.|   
|[Invoke-ASCmd](/sql/analysis-services/powershell/analysis-services-powershell-reference)|Wykonywanie skryptów TMSL.|

## <a name="row-filters"></a>Filtry wierszy  

Filtry wierszy definiują, które wiersze w tabeli mogą być przeszukiwane przez członków określonej roli. Filtry wierszy są zdefiniowane dla każdej tabeli w modelu przy użyciu formuły języka DAX.  
  
Można zdefiniować filtry wierszy tylko do odczytu i odczytu ról i uprawnień procesu. Domyślnie jeśli Filtr wierszy nie jest zdefiniowany dla konkretnej tabeli elementy członkowskie można badać wszystkie wiersze w tabeli, chyba że filtrowanie krzyżowe stosuje z innej tabeli.
  
 Filtry wierszy wymagają formuły języka DAX musi zwrócić wartość PRAWDA/FAŁSZ do definiowania wierszy, które mogą być przeszukiwane przez członków tej określonej roli. Nie można zbadać wierszy nie jest uwzględniona w formule języka DAX. Na przykład tabeli Klienci z następującym wierszem filtry wyrażenie *= klienci [Kraj] = "USA"* , członkowie roli Sprzedaż może zobaczyć tylko klientom w Stanach Zjednoczonych.  
  
Filtry wierszy mają zastosowanie do określonych wierszy i powiązane wiersze. Jeśli tabela ma wiele relacji, filtry są stosowane zabezpieczenia dla relacji, który jest aktywny. Filtry wierszy są zakończone z innych filtrach wiersza zdefiniowane dla powiązanych tabel, na przykład:  
  
|Tabela|Wyrażenie DAX|  
|-----------|--------------------|  
|Region|=Region[Country]="USA"|  
|ProductCategory|= ProductCategory [Name] = "Rowerów"|  
|Transakcje|= Transakcje [rok] = 2016|  
  
 Efektem sieciowym jest, że członkowie mogą wysyłać zapytania wiersze danych, w którym klient ma miejsce w USA, Kategoria produktu jest rowerów i rok jest 2016. Użytkownicy nie mogą badać transakcji poza USA, transakcje, które nie rowerów lub transakcje nie są w 2016, chyba że należą one do innej roli, która udziela te uprawnienia.
  
 Można użyć filtru, *=FALSE()* , aby odmówić dostępu do wszystkich wierszy dla całej tabeli.

## <a name="next-steps"></a>Kolejne kroki

  [Zarządzanie administratorami serwerów](analysis-services-server-admins.md)   
  [Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell](analysis-services-powershell.md)  
  [Tabular Model Scripting Skorowidz języka (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

