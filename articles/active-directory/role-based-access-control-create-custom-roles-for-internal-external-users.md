---
title: Tworzenie niestandardowych dostępu opartej na rolach ról kontroli i przypisać do użytkowników wewnętrznych i zewnętrznych na platformie Azure | Dokumentacja firmy Microsoft
description: Przypisz role RBAC niestandardowe utworzone przy użyciu programu PowerShell i interfejsu wiersza polecenia dla użytkowników wewnętrznych i zewnętrznych
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: kgremban
ms.assetid: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/20/2018
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: b60b30e3a5a4f5adec4fbef8c4e981ad034a7f6c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2018
---
# <a name="intro-on-role-based-access-control"></a>Wprowadzenie dotyczących kontroli dostępu opartej na rolach

Kontrola dostępu oparta na rolach to Azure portalu funkcja tylko stosowanie właściciele subskrypcji do przypisywania ról szczegółowego do innych użytkowników zarządzających zasobów dla określonych zakresów w swoim środowisku.

RBAC umożliwia lepsze zarządzanie zabezpieczeniami dla dużych organizacji oraz dla małych i średnich firmach praca z zewnętrznym współpracownikom, dostawców lub freelancers wymagających dostępu do określonych zasobów w danym środowisku, ale niekoniecznie całej infrastruktury lub dowolny zakresy związanych z rozliczeniami. RBAC umożliwia elastyczność będący właścicielem jedną subskrypcją platformy Azure zarządzanych przez administratora konta (usługi roli administrator na poziomie subskrypcji) i mieć wielu użytkowników zaproszenie do pracy w ramach tej samej subskrypcji, ale bez jakichkolwiek praw administracyjnych dla niego. Zarządzanie i rozliczeń funkcję RBAC okaże się, że czas i zarządzanie wydajne opcji korzystania z funkcji Azure w różnych scenariuszach.

## <a name="prerequisites"></a>Wymagania wstępne
W środowisku platformy Azure przy użyciu funkcji RBAC wymaga:

* Posiadanie autonomiczny subskrypcji platformy Azure powierzonych użytkownika jako właściciela (rola subskrypcji)
* Rola właściciela subskrypcji platformy Azure
* Ma dostęp do [portalu Azure](https://portal.azure.com)
* Upewnij się, że ma następujących dostawców zasobów w zarejestrowany dla subskrypcji użytkownika: **Microsoft.Authorization**. Aby uzyskać więcej informacji na temat rejestrowania dostawców zasobów, zobacz [dostawców usługi Resource Manager, regiony, wersje interfejsu API i schematów](../azure-resource-manager/resource-manager-supported-services.md).

> [!NOTE]
> Licencje usługi Azure Active Directory lub subskrypcji usługi Office 365 (na przykład: dostęp do usługi Azure Active Directory) pobranego z Centrum nie kwalifikuje się do przy użyciu funkcji RBAC Office 365 Admin.

## <a name="how-can-rbac-be-used"></a>Jak można użyć RBAC
RBAC można zastosować na trzy różne zakresy na platformie Azure. Z najwyższą zakresu na najniższym jeden są następujące:

* Subskrypcja (najwyższy)
* Grupa zasobów
* Zakres zasobów (najniższy poziom dostępu do oferty docelowe uprawnienia do zakresu poszczególnych zasobów platformy Azure)

## <a name="assign-rbac-roles-at-the-subscription-scope"></a>Przypisz role RBAC w zakresie subskrypcji
Istnieją dwie typowe przykłady dotyczące RBAC jest używana (między innymi):

* Użytkowników zewnętrznych z organizacji (nie jest częścią dzierżawy usługi Azure Active Directory dla użytkownika administracyjnego) zaproszenie do zarządzania niektórych zasobów lub całej subskrypcji
* Praca z użytkownikami w organizacji (są one częścią dzierżawy usługi Azure Active Directory użytkownika), ale należy do różnych zespołów lub grup, wymagających szczegółowego dostępu do całej subskrypcji lub do określonych grup zasobów lub zakresy zasobów w środowisku

## <a name="grant-access-at-a-subscription-level-for-a-user-outside-of-azure-active-directory"></a>Udziel dostępu na poziomie subskrypcji dla użytkownika poza usługą Azure Active Directory
Role RBAC może zostać przydzielony tylko przez **właścicieli** subskrypcji. W związku z tym administrator musi zalogować się jako użytkownik, który zawiera tę rolę wstępnie przypisany lub została utworzona subskrypcja platformy Azure.

W portalu Azure po zalogowaniu się jako administrator, wybierz "Subskrypcji" i wybierz jedno.
![Subskrypcja bloku w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/0.png) domyślnie, jeśli dla użytkownika administracyjnego kupiła subskrypcji platformy Azure, użytkownik będzie wyświetlany jako **administrator konta**, to jest rola subskrypcji. Aby uzyskać więcej informacji o rolach subskrypcji platformy Azure, zobacz [Dodawanie lub zmienianie ról administrator usługi Azure, które zarządzają subskrypcji lub usługi](/billing/billing-add-change-azure-subscription-administrator.md).

W tym przykładzie użytkownik "alflanigan@outlook.com" jest **właściciela** z "Bezpłatnej wersji próbnej" subskrypcji w usłudze AAD dzierżawy "Dzierżawy Azure Default". Ponieważ ten użytkownik jest twórca subskrypcji platformy Azure z początkowej Account Microsoft "Outlook" (Account Microsoft = programu Outlook, Live itp.) będzie domyślna nazwa domeny dla wszystkich innych użytkowników dodane w tej dzierżawie **"@alflaniganuoutlook.onmicrosoft.com"**. Zgodnie z projektem składni nowej domeny jest tworzony przez zestawienie nazwę użytkownika i domenę nazwę użytkownika, który utworzył dzierżawcy i dodawanie rozszerzenia **". onmicrosoft.com"**.
Ponadto użytkownicy mogą zalogować się przy użyciu niestandardowej nazwy domeny w dzierżawie po dodaniu i weryfikowanie jego dla nowej dzierżawy. Aby uzyskać więcej informacji na temat sposobu zweryfikować niestandardowej nazwy domeny w dzierżawie usługi Azure Active Directory, zobacz [Dodawanie niestandardowej nazwy domeny do katalogu](/active-directory/active-directory-add-domain).

W tym przykładzie katalog "Domyślna dzierżawa usługi Azure" zawiera tylko użytkownicy z nazwą domeny "@alflanigan.onmicrosoft.com".

Po wybraniu subskrypcji, administrator musi kliknij **kontroli dostępu (IAM)** , a następnie **dodania roli**.





![Funkcja IAM kontroli dostępu w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/1.png)





![Dodaj nowego użytkownika w funkcja IAM kontroli dostępu w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/2.png)

Następnym krokiem jest wybranie roli do przypisania i użytkownika, którego rola RBAC zostanie przypisana do. W **roli** menu rozwijanym administratora użytkownik widzi tylko wbudowane role RBAC, które są dostępne w systemie Azure. Aby uzyskać bardziej szczegółowe wyjaśnienia dotyczące poszczególnych ról i ich zakresy możliwe do przypisania, zobacz [wbudowanych ról dla kontroli dostępu](role-based-access-built-in-roles.md).

Następnie administrator musi dodać adres e-mail użytkownika zewnętrznego. Oczekiwane zachowanie jest dla użytkownika zewnętrznego, które nie są wyświetlani w istniejącej dzierżawy. Po Zaproszono użytkownika zewnętrznego, on będą widoczne w obszarze **subskrypcji > kontroli dostępu (IAM)** z wszystkich bieżących użytkowników, które są obecnie przypisane roli RBAC w zakresie subskrypcji.





![Dodaj uprawnienia do nowej roli RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/3.png)





![Lista ról RBAC na poziomie subskrypcji](./media/role-based-access-control-create-custom-roles-for-internal-external-users/4.png)

Użytkownik "chessercarlton@gmail.com" zaproszono jako **właściciela** dla subskrypcji "Bezpłatnej wersji próbnej". Po wysłaniu zaproszenia, zewnętrznych użytkownik otrzyma wiadomość e-mail z potwierdzeniem z link aktywacji.
![wiadomość e-mail z zaproszeniem dla roli RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/5.png)

Trwa spoza organizacji, nowy użytkownik nie ma żadnych istniejących atrybutów w katalogu "Domyślna dzierżawa usługi Azure". Będzie można utworzyć po uzyskaniu zgody użytkownika zewnętrznego mają być rejestrowane w katalogu, który jest skojarzony z subskrypcją został przydzielony do roli.





![wiadomość e-mail zaproszenia dla roli RBAC](./media/role-based-access-control-create-custom-roles-for-internal-external-users/6.png)

Pokazuje użytkownika zewnętrznego w dzierżawcy usługi Azure Active Directory od teraz jako użytkownik zewnętrzny i to można wyświetlić w portalu Azure.





![Użytkownicy portalu Azure usługi active directory bloku azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/7.png)



W **użytkowników** widoku, użytkownicy zewnętrzni mogą być rozpoznawane przez typ inną ikonę w portalu Azure.

Jednak udzielanie **właściciela** lub **współautora** dostępu do użytkownika zewnętrznego w **subskrypcji** zakresu, nie zezwala na dostęp do katalogu dla użytkownika administracyjnego, chyba że **administratora globalnego** pozwala. W ich właściwości użytkownika **typ użytkownika**, który ma dwie typowe parametry, **elementu członkowskiego** i **gościa** mogą zostać zidentyfikowane. Element członkowski jest użytkownik, który jest zarejestrowany w katalogu, gdy Gość jest użytkownikiem zaproszenie do katalogu z zewnętrznego źródła. Aby uzyskać więcej informacji, zobacz [jak Administratorzy usługi Azure Active Directory dodać użytkowników współpracy B2B](active-directory-b2b-admin-add-users.md).

> [!NOTE]
> Upewnij się, że po wprowadzeniu poświadczeń w portalu, użytkownik zewnętrzny wybiera do logowania się w poprawnym katalogu. Tego samego użytkownika może mieć dostęp do wielu katalogów i można wybrać jedną z nich, klikając nazwę użytkownika w góry po prawej stronie w portalu Azure a następnie wybierz odpowiedniego katalogu z listy rozwijanej.

Będąc gościa w katalogu użytkownika zewnętrznego mogą zarządzać zasobami wszystkich subskrypcji platformy Azure, ale nie można uzyskać dostępu do katalogu.





![dostęp ograniczony do portalu Azure usługi azure active directory](./media/role-based-access-control-create-custom-roles-for-internal-external-users/9.png)

Azure Active Directory i subskrypcji platformy Azure nie ma relacji relacji nadrzędny podrzędny, takich jak innych zasobów platformy Azure (na przykład: maszyn wirtualnych, sieci wirtualnych, aplikacje sieci web, magazynu itp.) z subskrypcją platformy Azure. Wszystkie ostatnie jest utworzony, zarządzane i rozliczany w ramach subskrypcji platformy Azure, podczas gdy subskrypcji platformy Azure jest używany do zarządzania dostępem do usługi Azure directory. Aby uzyskać więcej informacji, zobacz [subskrypcji jak Azure jest powiązana z usługą Azure AD](/active-directory/active-directory-how-subscriptions-associated-directory).

Z wszystkich wbudowane role RBAC **właściciela** i **współautora** oferują pełnego zarządzania dostęp do wszystkich zasobów w środowisku różnica, że współautora nie może tworzyć i usuwać nowe role RBAC. Inne role wbudowane, takich jak **współautora maszyny wirtualnej** oferować pełnego zarządzania dostęp tylko do zasobów, jest określany przez nazwę, niezależnie od tego **grupy zasobów** jest tworzona w.

Przypisywanie roli RBAC wbudowanych **Współautor·maszyny·wirtualnej** na poziomie subskrypcji, oznacza, że użytkownikowi przypisano rolę:

* Można wyświetlić wszystkich maszyn wirtualnych niezależnie od daty wdrożenia i grupy zasobów, które są częścią
* Ma dostęp do pełnego zarządzania do maszyn wirtualnych w subskrypcji
* Nie można wyświetlić inne typy zasobów w subskrypcji
* Nie można wykonać operacji zmiany z punktu widzenia rozliczeń

## <a name="assign-a-built-in-rbac-role-to-an-external-user"></a>Przypisywanie roli RBAC wbudowanych do użytkownika zewnętrznego
Dla innego scenariusza, w tym teście użytkownika zewnętrznego "alflanigan@gmail.com" zostanie dodany jako **Współautor·maszyny·wirtualnej**.




![wbudowana Rola współautora maszyny wirtualnej](./media/role-based-access-control-create-custom-roles-for-internal-external-users/11.png)

Normalne zachowanie dla tego użytkownika zewnętrznego z tą rolą wbudowanych jest wyświetlanie i zarządzanie nimi tylko maszyny wirtualne i ich sąsiadujących ze sobą Menedżer zasobów tylko zasoby niezbędne podczas wdrażania. Zgodnie z projektem te role ograniczone zapewniają dostęp tylko do ich zasobów odpowiedniego utworzone w portalu Azure.



![Omówienie roli współautora maszyny wirtualnej w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/12.png)

## <a name="grant-access-at-a-subscription-level-for-a-user-in-the-same-directory"></a>Udziel dostępu na poziomie subskrypcji dla użytkownika w tym samym katalogu
Przepływ procesu jest taki sam jak dodawanie użytkownika zewnętrznego, zarówno z perspektywy administracyjnej przyznania roli RBAC, a także użytkownika zostanie im przyznany dostęp do roli. Różnica polega na tym że zaproszonych użytkownik nie będzie otrzymywać żadnych zaproszeń do skorzystania z poczty e-mail, jak wszystkie zakresy zasobów w subskrypcji będą dostępne na pulpicie nawigacyjnym po zalogowaniu się.

## <a name="assign-rbac-roles-at-the-resource-group-scope"></a>Przypisz role RBAC w zakresie grupy zasobów
Przypisywanie roli RBAC **grupy zasobów** zakres ma taki sam proces przypisywania roli na poziomie subskrypcji dla obu typów użytkownicy — zewnętrznym lub wewnętrznym (część z tym samym katalogu). Użytkownicy, którym przypisano rolę RBAC ma zobacz w swoim środowisku tylko grupy zasobów z przypisanym dostępem z **grup zasobów** ikonę w portalu Azure.

## <a name="assign-rbac-roles-at-the-resource-scope"></a>Przypisz role RBAC w zakresie zasobów
Przypisywanie roli RBAC w zakresie zasobów na platformie Azure mają identyczne proces przypisywania roli na poziomie subskrypcji lub na poziomie grupy zasobów, po tym samym przepływie pracy w obydwu scenariuszach. Ponownie, użytkowników, którym przypisano rolę RBAC można wyświetlanie tylko tych elementów, które przypisano dostępu do w **wszystkie zasoby** kartę lub bezpośrednio w ich pulpitu nawigacyjnego.

Istotnym elementem do RBAC zarówno w zakresie grupy zasobów lub zasobów zakresie jest przeznaczony dla użytkowników upewnić się zarejestrować się w poprawnym katalogu.





![katalog logowania w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/13.png)

## <a name="assign-rbac-roles-for-an-azure-active-directory-group"></a>Przypisz role RBAC dla grupy usługi Azure Active Directory
Wszystkie scenariusze na trzy różne zakresy na platformie Azure przy użyciu funkcji RBAC oferują uprawnienie Zarządzanie, wdrażanie i administrowanie różnych zasobów jako przypisany użytkownik bez konieczności zarządzania osobiste subskrypcji. Niezależnie od przypisano rolę RBAC dla subskrypcji, grupy zasobów lub zasobów zakresu, wszystkie zasoby utworzone dalej przez przypisanych użytkowników są rozliczane zgodnie z jedną subskrypcją platformy Azure, której użytkownicy mają dostęp do. Dzięki temu użytkowników, którzy mają rozliczeń uprawnień administratora dla całej subskrypcji platformy Azure ma pełny przegląd zużycia, niezależnie od tego, kto jest zarządzania zasobami.

W przypadku większych organizacji role RBAC można zastosować w taki sam sposób dla uwzględnieniu perspektywy administrator chce szczegółowego dostęp dla zespołów lub całego działów, indywidualnie dla każdego użytkownika, w związku z tym uwzględnieniu bardzo czas i zarządzanie wydajne opcja grup usługi Azure Active Directory. Przykład ilustrujący **współautora** rola została dodana do jednej z grup w dzierżawie na poziomie subskrypcji.





![Dodaj rolę RBAC dla grup usługi AAD](./media/role-based-access-control-create-custom-roles-for-internal-external-users/14.png)

Grupy te są grup zabezpieczeń, które są udostępniane i zarządzane tylko w ramach usługi Azure Active Directory.

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-powershell"></a>Utwórz niestandardową rolę RBAC można otworzyć żądania obsługi przy użyciu programu PowerShell
Wbudowane role, które są dostępne w systemie Azure zapewnia określone poziomy uprawnień na podstawie dostępnych zasobów w środowisku. Jednak jeśli wbudowane role nie spełniają potrzeb, można tworzyć role niestandardowe.

Aby utworzyć niestandardową rolę, może rozpoczynać się od wbudowanej roli, go edytować i następnie utwórz nową rolę. Na przykład wbudowana **czytnika** roli został dostosowany do Zezwalaj użytkownikom z możliwością otwarcia żądania pomocy technicznej.

W programie PowerShell, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenie, aby wyeksportować **czytnika** roli w formacie JSON.

```powershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

Poniżej przedstawiono dane wyjściowe JSON dla roli czytnika.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Następnie możesz edytować dane wyjściowe do utworzenia niestandardowej roli zabezpieczeń JSON.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Typowa rola składa się z trzech głównych sekcji, **akcje**, **NotActions**, i **AssignableScopes**.

**Akcji** sekcja zawiera informacje o dozwolonych operacji w roli. W takim przypadku można utworzyć obsługi biletów, **Microsoft.Support/&ast;**  operacji musi zostać dodany. Należy zrozumieć, że każda operacja ma zostać udostępnione od dostawcy zasobów. Aby uzyskać listę działań dla dostawcy zasobów, można użyć [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) polecenie lub zobacz [operacji dostawcy zasobów usługi Azure Resource Manager](role-based-access-control-resource-provider-operations.md).

Aby ograniczyć wszystkie akcje dla określonej roli, dostawców zasobów są wyświetlane w obszarze **NotActions** sekcji.
Jest to konieczne, że rola zawiera jawne subskrypcji identyfikatorów, w którym została użyta. Identyfikatory subskrypcji są wyświetlane w obszarze **AssignableScopes**, w przeciwnym razie będzie nie można zaimportować rolę do subskrypcji.

Aby utworzyć niestandardowe roli, należy użyć [AzureRmRoleDefinition nowy](/powershell/module/azurerm.resources/new-azurermroledefinition) poleceń i podaj zaktualizowany plik definicji roli JSON.

```powershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

W tym przykładzie nazwę tej niestandardowej roli zabezpieczeń jest "biletami pomocy technicznej czytnika poziom dostępu". Umożliwia użytkownikowi przeglądanie wszystko w subskrypcji, a także żądania pomocy technicznej Otwórz.

> [!NOTE]
> Tylko dwa wbudowane role, które umożliwiają użytkownikom otwarcia żądania pomocy technicznej są **właściciela** i **współautora**. Użytkownik może mieć możliwość otwarcia żądania pomocy technicznej on należy przypisać rolę w zakresie subskrypcji, ponieważ wszystkie żądania pomocy technicznej są tworzone na podstawie subskrypcji platformy Azure.

Nowa rola niestandardowy jest teraz dostępna w portalu Azure i mogą być przypisane do użytkowników.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń zaimportowany w portalu Azure](./media/role-based-access-control-create-custom-roles-for-internal-external-users/18.png)

![Zrzut ekranu przedstawiający przypisywanie niestandardowej roli zabezpieczeń zaimportowane do użytkownika w tym samym katalogu](./media/role-based-access-control-create-custom-roles-for-internal-external-users/19.png)

![Zrzut ekranu przedstawiający uprawnienia niestandardowe importowanych roli](./media/role-based-access-control-create-custom-roles-for-internal-external-users/20.png)

Użytkownicy z tą rolą niestandardowe można tworzyć nowe żądania pomocy technicznej.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń tworzenia żądań obsługi](./media/role-based-access-control-create-custom-roles-for-internal-external-users/21.png)

Użytkownicy z tę rolę niestandardową nie może wykonywać inne akcje, takie jak tworzenie maszyn wirtualnych lub tworzenia grup zasobów.

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń nie można utworzyć maszyny wirtualne](./media/role-based-access-control-create-custom-roles-for-internal-external-users/22.png)

![Zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń nie można utworzyć nowego RGs](./media/role-based-access-control-create-custom-roles-for-internal-external-users/23.png)

## <a name="create-a-custom-rbac-role-to-open-support-requests-using-azure-cli"></a>Utwórz niestandardową rolę RBAC można otworzyć żądania obsługi przy użyciu wiersza polecenia platformy Azure

Kroki, aby utworzyć niestandardową rolę przy użyciu interfejsu wiersza polecenia Azure przypominają przy użyciu programu PowerShell, różni się dane wyjściowe JSON.

Na przykład można uruchomić z wbudowanej **czytnika** roli. Aby wyświetlić listę działań rolę czytelnika, użyj [listy definicji roli az](/cli/azure/role/definition#az_role_definition_list) polecenia.

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Utwórz plik JSON o następującym formacie. **Microsoft.Support/&ast;**  operacji został dodany w **akcje** sekcjach, aby ten użytkownik może otworzyć żądania pomocy technicznej pozostawiając być do odczytu. Należy dodać identyfikator subskrypcji, w którym ta rola będzie używany w **AssignableScopes** sekcji.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Aby utworzyć niestandardowe roli, należy użyć [utworzenia definicji roli az](/cli/azure/role/definition#az_role_definition_create) polecenia.

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

Nowa rola niestandardowy jest teraz dostępna w portalu Azure i procesu, aby użyć tej roli są takie same jak w poprzedniej sekcji środowiska PowerShell.

![Azure portalu zrzut ekranu przedstawiający niestandardowej roli zabezpieczeń utworzone za pomocą interfejsu wiersza polecenia 1.0](./media/role-based-access-control-create-custom-roles-for-internal-external-users/26.png)
