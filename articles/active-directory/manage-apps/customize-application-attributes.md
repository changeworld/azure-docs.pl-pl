---
title: Dostosowywanie mapowań atrybutów usługi Azure AD | Microsoft Docs
description: Dowiedz się, jakie mapowania atrybutów dla aplikacji SaaS w Azure Active Directory są sposobami ich modyfikacji w celu zaspokajania potrzeb firmy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef3d6a47986056925f9964638c9c7192341ca5f9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240988"
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Dostosowywanie mapowania atrybutów aprowizacji użytkowników dla aplikacji SaaS w Azure Active Directory

Microsoft Azure AD zapewnia pomoc techniczną dla aprowizacji użytkowników do aplikacji SaaS innych firm, takich jak Salesforce, G Suite i innych. W przypadku włączenia aprowizacji użytkowników dla aplikacji SaaS innej firmy Azure Portal kontroluje wartości atrybutów za pomocą mapowań atrybutów.

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD i obiektami użytkowników aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów razem z użytkownikami, takimi jak grupy.

Domyślne mapowania atrybutów można dostosować zgodnie z potrzebami biznesowymi. W związku z tym można zmienić lub usunąć istniejące mapowania atrybutów lub utworzyć nowe mapowania atrybutów.

## <a name="editing-user-attribute-mappings"></a>Edytowanie atrybutów użytkownika — mapowania

Wykonaj następujące kroki, aby uzyskać dostęp do funkcji **mapowania** dotyczącej aprowizacji użytkowników:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com).
1. W okienku po lewej stronie wybierz pozycję **aplikacje dla przedsiębiorstw** . Zostanie wyświetlona lista wszystkich skonfigurowanych aplikacji, w tym aplikacji, które zostały dodane z galerii.
1. Wybierz dowolną aplikację, aby załadować okienko zarządzania aplikacjami, w którym można wyświetlać raporty i zarządzać ustawieniami aplikacji.
1. Wybierz opcję **aprowizacji** , aby zarządzać ustawieniami aprowizacji konta użytkownika dla wybranej aplikacji.
1. Rozwiń węzeł **mapowania** , aby wyświetlić i edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową. Jeśli aplikacja docelowa obsługuje tę funkcję, w tej sekcji można opcjonalnie skonfigurować obsługę administracyjną grup i kont użytkowników.

   ![Używanie mapowań do wyświetlania i edytowania atrybutów użytkownika](./media/customize-application-attributes/21.png)

1. Wybierz konfigurację **mapowań** , aby otworzyć ekran mapowanie powiązanego **atrybutu** . Niektóre mapowania atrybutów są wymagane przez aplikację SaaS do poprawnego działania. W przypadku wymaganych atrybutów funkcja **usuwania** jest niedostępna.

   ![Używanie mapowania atrybutów do konfigurowania mapowań atrybutów dla aplikacji](./media/customize-application-attributes/22.png)

   Na tym zrzucie ekranu można zobaczyć, że atrybut **username** obiektu zarządzanego w usłudze Salesforce jest wypełniony wartością **userPrincipalName** połączonego obiektu Azure Active Directory.

1. Wybierz istniejące **Mapowanie atrybutu** , aby otworzyć ekran **Edytuj atrybut** . W tym miejscu można edytować atrybuty użytkownika, które przepływają między usługą Azure AD a aplikacją docelową.

   ![Edytowanie atrybutów użytkownika przy użyciu atrybutu Edytuj](./media/customize-application-attributes/23.png)

### <a name="understanding-attribute-mapping-types"></a>Informacje o typach mapowań atrybutów

Mapowania atrybutów umożliwiają kontrolowanie sposobu, w jaki atrybuty są wypełniane w aplikacji SaaS innej firmy.
Obsługiwane są cztery różne typy mapowania:

- **Direct** — atrybut docelowy jest wypełniany wartością atrybutu obiektu połączonego w usłudze Azure AD.
- **Stała** — atrybut docelowy jest wypełniony określonym określonym ciągiem.
- **Expression** — atrybut target jest wypełniany w oparciu o wynik wyrażenia przypominającego skrypt.
  Aby uzyskać więcej informacji, zobacz [Pisanie wyrażeń mapowania atrybutów w Azure Active Directory](functions-for-customizing-application-data.md).
- **Brak** — atrybut docelowy nie został zmodyfikowany. Jeśli jednak atrybut target jest kiedykolwiek pusty, zostanie wypełniony wartością domyślną, którą określisz.

Podobnie jak te cztery podstawowe typy, niestandardowe mapowania atrybutów obsługują koncepcję opcjonalnego przypisania wartości **domyślnych** . Przypisanie wartości domyślnej gwarantuje, że atrybut docelowy zostanie wypełniony wartością, jeśli nie istnieje wartość w usłudze Azure AD lub w obiekcie docelowym. Najbardziej typową konfiguracją jest pozostawienie tej pustej.

### <a name="understanding-attribute-mapping-properties"></a>Informacje o właściwościach mapowania atrybutów

W poprzedniej sekcji wprowadzono już do właściwości Typ mapowania atrybutu.
Wraz z tą właściwością mapowania atrybutów obsługują również następujące atrybuty:

- **Atrybut źródłowy** — atrybut użytkownika z systemu źródłowego (przykład: Azure Active Directory).
- **Attribute** — atrybut użytkownika w systemie docelowym (przykład: usługi ServiceNow).
- **Dopasowywanie obiektów przy użyciu tego atrybutu** — określa, czy mapowanie ma być używane do unikatowego identyfikowania użytkowników między systemami źródłowymi i docelowymi. Zwykle jest ona ustawiana w atrybucie userPrincipalName lub mail w usłudze Azure AD, która jest zwykle mapowana na pole username w aplikacji docelowej.
- **Priorytet dopasowania** — można ustawić wiele pasujących atrybutów. Jeśli istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.
- **Zastosuj to mapowanie**
  - **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika.
  - **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko w przypadku akcji tworzenia użytkownika.

## <a name="editing-group-attribute-mappings"></a>Edytowanie atrybutów grupy — mapowania

Wybrana liczba aplikacji, takich jak usługi ServiceNow, Box i G Suite, umożliwia obsługę administracyjną obiektów grup i obiektów użytkowników. Obiekty grupy mogą zawierać właściwości grupy, takie jak nazwy wyświetlane i aliasy poczty e-mail, wraz z członkami grupy.

![Przykład pokazuje usługi ServiceNow z zainicjowaną grupą i obiektami użytkownika](./media/customize-application-attributes/24.png)

Inicjowanie obsługi grup można opcjonalnie włączyć lub wyłączyć, wybierając mapowanie grupy w obszarze **mapowania**, a ustawienie opcji ma być **włączone** na ekranie **Mapowanie atrybutu** .

Atrybuty, które są obsługiwane jako część obiektów grupy można dostosować w taki sam sposób jak obiekty użytkownika, opisane wcześniej. 

> [!TIP]
> Inicjowanie obsługi obiektów grup (właściwości i członków) jest odrębnym pojęciem [przypisywania grup](assign-user-or-group-access-portal.md) do aplikacji. Możliwe jest przypisanie grupy do aplikacji, ale tylko udostępnianie obiektów użytkowników zawartych w grupie. Nie jest wymagane Inicjowanie obsługi obiektów w grupach w przypisaniach.

## <a name="editing-the-list-of-supported-attributes"></a>Edytowanie listy obsługiwanych atrybutów

Atrybuty użytkownika obsługiwane przez daną aplikację są wstępnie skonfigurowane. Interfejsy API zarządzania użytkownikami w większości zastosowań nie obsługują odnajdywania schematów. W związku z tym usługa Azure AD Provisioning nie może dynamicznie wygenerować listy obsługiwanych atrybutów przez wykonywanie wywołań do aplikacji.

Niektóre aplikacje obsługują jednak atrybuty niestandardowe, a usługa Azure AD Provisioning może odczytywać i zapisywać w atrybutach niestandardowych. Aby wprowadzić definicje do Azure Portal, zaznacz pole wyboru **Pokaż opcje zaawansowane** u dołu ekranu **mapowania atrybutów** , a następnie wybierz pozycję **Edytuj listę atrybutów dla** swojej aplikacji.

Aplikacje i systemy obsługujące Dostosowywanie listy atrybutów obejmują:

- Salesforce
- ServiceNow
- Workday
- Azure Active Directory (obsługiwane są[domyślne atrybuty usługi Azure AD interfejs API programu Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity) i niestandardowe rozszerzenia katalogu)
- Aplikacje obsługujące [standard scim 2,0](https://tools.ietf.org/html/rfc7643), w których atrybuty zdefiniowane w [podstawowym schemacie](https://tools.ietf.org/html/rfc7643) muszą być dodane

> [!NOTE]
> Edytowanie listy obsługiwanych atrybutów jest zalecane tylko dla administratorów, którzy dostosowali schemat aplikacji i systemów, i posiadają pierwszą wiedzę o sposobie definiowania ich atrybutów niestandardowych. Czasami wymaga to znajomości interfejsów API i narzędzi programistycznych udostępnianych przez aplikację lub system.

Podczas edytowania listy obsługiwanych atrybutów są dostępne następujące właściwości:

- **Nazwa** — Nazwa systemowa atrybutu, zgodnie z definicją w schemacie obiektu docelowego.
- **Typ** — typ danych przechowywanych w atrybutach, zgodnie z definicją w schemacie obiektu docelowego, który może być jednym z następujących typów:
  - Atrybut *Binary* zawiera dane binarne.
  - Wartość *logiczna* -Attribute zawiera wartości true lub false.
  - *Właściwość DateTime* -Attribute zawiera ciąg daty.
  - Wartość *całkowita* — atrybut zawiera liczbę całkowitą.
  - Atrybut *Reference* -Attribute zawiera identyfikator, który odwołuje się do wartości przechowywanej w innej tabeli w aplikacji docelowej.
  - Atrybut *String* zawiera ciąg tekstowy.
- **Klucz podstawowy?** -Czy atrybut jest zdefiniowany jako pole klucza podstawowego w schemacie obiektu docelowego.
- **Wymagane?** -Czy atrybut jest wymagany do wypełnienia w aplikacji lub systemie docelowym.
- **Wiele wartości?** -Czy atrybut obsługuje wiele wartości.
- **Dokładnie przypadek?** -Czy wartości atrybutów są oceniane w sposób uwzględniający wielkość liter.
- **Wyrażenie interfejsu API** — nie używaj, o ile nie zostanie to zrobione przez dokumentację określonego łącznika aprowizacji (na przykład Workday).
- **Atrybut obiektu przywoływany** — jeśli jest to atrybut typu referencyjnego, to menu umożliwia wybranie tabeli i atrybutu w aplikacji docelowej zawierającej wartość skojarzoną z atrybutem. Na przykład jeśli masz atrybut o nazwie "Department", którego przechowywana wartość odwołuje się do obiektu w oddzielnej tabeli "działS", wybierz pozycję "Departments.Name". Tabele odwołań i pola identyfikatora podstawowego obsługiwane przez daną aplikację są wstępnie skonfigurowane i obecnie nie można ich edytować za pomocą Azure Portal, ale można je edytować przy użyciu [interfejs API programu Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-configure-with-custom-target-attributes).

Aby dodać nowy atrybut, przewiń do końca listy obsługiwanych atrybutów, wypełnij pola powyżej przy użyciu dostarczonych danych wejściowych, a następnie wybierz pozycję **Dodaj atrybut**. Wybierz pozycję **Zapisz** po zakończeniu dodawania atrybutów. Następnie należy ponownie załadować kartę **aprowizacji** , aby nowe atrybuty były dostępne w edytorze mapowania atrybutów.

## <a name="restoring-the-default-attributes-and-attribute-mappings"></a>Przywracanie domyślnych atrybutów i mapowań atrybutów

Jeśli trzeba zacząć od początku i zresetować istniejące mapowania z powrotem do stanu domyślnego, można zaznaczyć pole wyboru **Przywróć domyślne mapowania** i zapisać konfigurację. Spowoduje to ustawienie wszystkich mapowań, tak jakby aplikacja została dodana do dzierżawy usługi Azure AD z galerii aplikacji.

Wybranie tej opcji spowoduje skuteczną ponowną synchronizację wszystkich użytkowników, gdy usługa aprowizacji jest uruchomiona.

> [!IMPORTANT]
> Przed wywołaniem tej opcji zdecydowanie zalecamy ustawienie **stanu aprowizacji** na **wyłączony** .

## <a name="what-you-should-know"></a>Co należy wiedzieć

- Microsoft Azure AD zapewnia wydajną implementację procesu synchronizacji. W środowisku zainicjowanym tylko obiekty wymagające aktualizacji są przetwarzane podczas cyklu synchronizacji.
- Aktualizacja mapowań atrybutów ma wpływ na wydajność cyklu synchronizacji. Aktualizacja konfiguracji mapowania atrybutów wymaga ponownego oszacowania wszystkich zarządzanych obiektów.
- Zalecanym najlepszym rozwiązaniem jest pozostawienie w minimalnym zakresie liczby kolejnych zmian mapowań atrybutów.
- Dodawanie atrybutu Photo do aprowizacji do aplikacji nie jest obecnie obsługiwane, ponieważ nie można określić formatu synchronizacji zdjęcia. Możesz zażądać funkcji na [głos użytkownika](https://feedback.azure.com/forums/169401-azure-active-directory)
- Atrybut IsSoftDeleted jest często częścią domyślnych mapowań aplikacji. IsSoftdeleted może być spełniony w jednym z czterech scenariuszy (użytkownik jest poza zakresem, ponieważ nie jest przypisany do aplikacji, użytkownik jest poza zakresem ze względu na niespełnienie filtru określania zakresu, użytkownik został usunięty z usługi Azure AD lub właściwość AccountEnabled jest ustawiona na wartość false.  na użytkowniku). 
- Usługa Azure AD Provisioning nie obsługuje inicjowania obsługi wartości null.

## <a name="next-steps"></a>Następne kroki

- [Automatyzacja aprowizacji użytkowników/anulowania obsługi administracyjnej w aplikacjach SaaS](user-provisioning.md)
- [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
- [Filtry zakresu dla aprowizacji użytkowników](define-conditional-rules-for-provisioning-user-accounts.md)
- [Włączanie automatycznej aprowizacji użytkowników i grup z usługi Azure Active Directory do aplikacji przy użyciu SCIM](use-scim-to-provision-users-and-groups.md)
- [Lista samouczków dotyczących integrowania aplikacji SaaS](../saas-apps/tutorial-list.md)
