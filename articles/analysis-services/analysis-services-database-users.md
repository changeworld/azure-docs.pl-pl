---
title: Zarządzanie ról bazy danych i użytkowników w usłudze Azure Analysis Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zarządzać ról bazy danych i użytkowników na serwerze usług Analysis Services na platformie Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8870c4199d5f24d1e8d07bc97d61a09c07052c1e
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307983"
---
# <a name="manage-database-roles-and-users"></a>Zarządzanie ról bazy danych i użytkowników

Na poziomie bazy danych modelu wszyscy użytkownicy muszą należeć do roli. Role definiują użytkowników z uprawnieniami określonego dla bazy danych modelu. Dowolnemu użytkownikowi lub grupie zabezpieczeń dodany do roli musi mieć konto w dzierżawie usługi Azure AD w tej samej subskrypcji co serwer. 

Sposób definiowania ról różni się w zależności od używanego narzędzia, ale efekt jest taki sam.

Uprawnienia roli obejmują:
*  **Administrator** — użytkownicy mają pełne uprawnienia do bazy danych. Role bazy danych z uprawnieniami administratora różnią się od administratorów serwera.
*  **Proces** — użytkownicy mogą połączyć się z wykonywać operacje przetwarzania w bazie danych i analizowanie danych bazy danych modelu.
*  **Odczyt** — użytkownicy mogą używać aplikacji klienta do nawiązania połączenia i analizowania danych bazy danych modelu.

Podczas tworzenia projektu modelu tabelarycznego, tworzyć role i dodać użytkowników lub grupy do tych ról za pomocą menedżera ról programu SSDT. Po wdrożeniu na serwerze, użyj narzędzia SSMS, [poleceń cmdlet programu PowerShell usługi analizy](https://msdn.microsoft.com/library/hh758425.aspx), lub [język skryptów modelu tabelarycznego](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL), aby dodać lub usunąć role i członkowie.

> [!NOTE]
> Grupy zabezpieczeń musi mieć `MailEnabled` ustawioną właściwość `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Aby dodać lub zarządzania rolami i użytkownicy programu SSDT  
  
1.  Programu SSDT > **Eksplorator modelu tabelarycznego**, kliknij prawym przyciskiem myszy **ról**.  
  
2.  W **Menedżerze ról** kliknij przycisk **Nowa**.  
  
3.  Wpisz nazwę roli.  
  
     Domyślnie nazwa domyślna rola jest przyrostowo numerowane dla każdej nowej roli. Zaleca się, że wpisz nazwę, która jasno identyfikuje typ elementu członkowskiego, na przykład menedżerów finansowych lub specjalistami w zasoby ludzkie.  
  
4.  Wybierz jedną z następujących uprawnień:  
  
    |Uprawnienie|Opis|  
    |----------------|-----------------|  
    |**Brak**|Elementy członkowskie nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
    |**Odczyt**|Członkowie mogą zapytania na danych (w oparciu filtrów wierszy), ale nie można zmodyfikować schematu modelu.|  
    |**Odczyt i procesu**|Elementy członkowskie danych (oparte na poziomie wiersza filtry) oraz wykonywania operacji procesu i przetwórz wszystko można zapytania, ale nie można zmodyfikować schematu modelu.|  
    |**Proces**|Elementy członkowskie można uruchomić proces i procesu wszystkie operacje. Nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
    |**Administrator**|Elementy członkowskie można zmodyfikować schematu modelu i wyszukiwać wszystkie dane.|   
  
5.  W przypadku roli tworzenie ma odczytu lub uprawnienia odczytu i procesu, można dodać filtrów wierszy przy użyciu formuły języka DAX. Kliknij przycisk **filtrów wierszy** , a następnie wybierz tabelę, a następnie kliknij **filtru DAX** pola, a następnie wpisz formułę języka DAX.
  
6.  Kliknij przycisk **członków** > **Dodaj zewnętrzne**.  
  
8.  W **dodawania zewnętrznego członka**, wprowadź użytkowników lub grup w dzierżawie usługi Azure AD za pomocą adresu e-mail. Po kliknij przycisk OK i zamknąć Menedżera ról, ról i członkowie roli są widoczne w Eksploratorze modelu tabelarycznego. 
 
     ![Role i użytkownicy w Eksploratorze modelu tabelarycznego](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Wdrażanie z serwerem usług Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Aby dodać lub zarządzania rolami i użytkowników w programie SSMS
Dodawanie ról i użytkowników do bazy danych modelu wdrożone, można musi być połączone z serwerem administrator serwera lub jest już w roli bazy danych z uprawnieniami administratora.

1. W obiekcie Exporer, kliknij prawym przyciskiem myszy **ról** > **nową rolę**.

2. W **Utwórz rolę**, wprowadź nazwę roli i opis.

3. Wybierz uprawnienia.
   |Uprawnienie|Opis|  
   |----------------|-----------------|  
   |**Pełna kontrola (Administrator)**|Członkowie mogą modyfikować schematu modelu przetwarzania i można zbadać wszystkie dane.| 
   |**Proces bazy danych**|Elementy członkowskie można uruchomić proces i procesu wszystkie operacje. Nie można zmodyfikować schematu modelu i nie można wykonać zapytania na danych.|  
   |**Odczyt**|Członkowie mogą zapytania na danych (w oparciu filtrów wierszy), ale nie można zmodyfikować schematu modelu.|  
  
4. Kliknij przycisk **członkostwa**, wprowadź użytkownika lub grupy w dzierżawie usługi Azure AD za pomocą adresu e-mail.

     ![Dodawanie użytkownika](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Jeśli rola tworzonego ma uprawnień do odczytu, możesz dodać filtrów wierszy przy użyciu formuły języka DAX. Kliknij przycisk **filtrów wierszy**, wybierz tabelę, a następnie wpisz formułę języka DAX w **filtru DAX** pola. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Dodawanie ról i użytkowników przy użyciu skryptu TMSL
W oknie XMLA w programie SSMS lub przy użyciu programu PowerShell, należy uruchomić skrypt TMSL. Użyj [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) polecenia i [ról](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) obiektu.

**Przykładowy skrypt TMSL**

W tym przykładzie użytkownik zewnętrzny B2B i grupy są dodawane do roli analityk z uprawnieniami odczytu SalesBI bazy danych. Zarówno użytkownika zewnętrznego, jak i grupa musi być w tej samej dzierżawy usługi Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Dodawanie ról i użytkowników przy użyciu programu PowerShell
[SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modułu udostępnia polecenia cmdlet do bazy danych specyficznych dla zadań zarządzania i cmdlet Invoke-ASCmd ogólnego przeznaczenia akceptuje zapytań tabelarycznych modelu skryptów języka (TMSL) lub skryptu. Następujące polecenia cmdlet są używane do zarządzania rolami bazy danych i użytkowników.
  
|Polecenie cmdlet|Opis|
|------------|-----------------| 
|[Dodaj RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Dodaj członka do roli bazy danych.| 
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Usuwanie członka z roli bazy danych.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Wykonanie skryptu TMSL.|

## <a name="row-filters"></a>Filtrów wierszy  
Filtry wiersza definiują, które wiersze w tabeli mogą być przeszukiwane przez członków określonej roli. Filtrów wierszy są definiowane dla każdej tabeli w modelu przy użyciu formuły języka DAX.  
  
Filtrów wierszy można zdefiniować tylko do odczytu i odczytu ról i uprawnień procesu. Domyślnie jeśli filtr wiersza nie jest zdefiniowany dla danej tabeli, elementy Członkowskie mogą wysyłać zapytania wszystkie wiersze w tabeli, chyba że filtrowania krzyżowego stosuje z innej tabeli.
  
 Filtry wiersza wymagają formuły języka DAX musi zwrócić wartość TRUE/FALSE, aby określić wiersze, które mogą być przeszukiwane przez członków tej konkretnej roli. Nie można zbadać wierszy, które nie są uwzględnione w formule języka DAX. Na przykład tabela klientów o następujący wiersz filtruje wyrażenia *= klientów [Kraj] = "USA"*, członkowie roli sprzedaży może zobaczyć tylko klientów w USA.  
  
Filtrów wierszy są stosowane do określonych wierszy i powiązane wiersze. Tabela ma wiele relacji, filtry stosowane zabezpieczeń dla relacji, który jest aktywny. Filtrów wierszy są zakończone z innych filtrach wiersza zdefiniowane dla powiązane tabele, na przykład:  
  
|Tabela|Wyrażenie DAX|  
|-----------|--------------------|  
|Region|=Region[Country]=”USA”|  
|ProductCategory|= ProductCategory [nazwa] = "Rowerów"|  
|Transakcje|= Transakcje [rok] = 2016|  
  
 Net powoduje, że członkowie można zbadać wiersze danych, gdy klient jest w USA, Kategoria produktu jest rowerów i rok jest 2016. Użytkownicy nie mogą badać transakcji poza USA, transakcje, które nie znajdują się nie rowerów lub transakcji w 2016, chyba że są one członkiem innej roli, która udziela te uprawnienia.
  
 Możesz użyć opcji Filtruj *=FALSE()*, aby odmówić dostępu do wszystkich wierszy dla całej tabeli.

## <a name="next-steps"></a>Kolejne kroki
  [Administratorzy serwerów zarządzania](analysis-services-server-admins.md)   
  [Zarządzanie usług Azure Analysis Services przy użyciu programu PowerShell](analysis-services-powershell.md)  
  [Model tabelaryczny skryptów materiały referencyjne dotyczące języka (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

