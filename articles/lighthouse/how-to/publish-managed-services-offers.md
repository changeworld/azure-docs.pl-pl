---
title: Publikowanie oferty usług zarządzanych w portalu Azure Marketplace
description: Dowiedz się, jak opublikować ofertę usługi zarządzanej, służy do dołączania klientów na platformę Azure delegowane zarządzanie zasobami.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: bb2f26a170bbd60eb927bd00f6def7d033fafee9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809848"
---
# <a name="publish-a-managed-services-offer-to-azure-marketplace"></a>Publikowanie oferty usług zarządzanych w portalu Azure Marketplace

W tym artykule dowiesz się, jak opublikować ofertę usługi zarządzane publicznych lub prywatnych [portalu Azure Marketplace](https://azuremarketplace.microsoft.com) przy użyciu [portalu Cloud Partner](https://cloudpartner.azure.com/), umożliwiając klient, który nabywa ofertę, które mają zostać dołączone delegowane zarządzanie zasobami na platformie Azure. 

> [!NOTE]
> Musisz mieć prawidłowe [konta w Centrum partnerskim](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) można tworzyć i publikować tych ofert. Jeśli nie masz już konto [procesu rejestracji](https://aka.ms/joinmarketplace) przeprowadzą Cię przez kroki tworzenia konta w Centrum partnerskim i rejestrowanie w programie komercyjnych portalu Marketplace. Identyfikator sieci Microsoft Partner Network (MPN) będzie [automatycznie skojarzone](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started) dzięki ofertom publikowania Śledź swoje oddziaływanie na podczas pracy z klientami.
>
> Jeśli nie chcesz opublikować ofertę w portalu Azure Marketplace, możesz dołączać nowych klientów ręcznie przy użyciu szablonów usługi Azure Resource Manager. Aby uzyskać więcej informacji, zobacz [dołączanie klientów na platformie Azure delegowane zarządzanie zasobami](onboard-customer.md).

Publikowanie usług zarządzanych przez oferty jest podobna do publikowania dowolny inny typ oferty w portalu Azure Marketplace. Aby dowiedzieć się więcej na temat tego procesu, zobacz [portalu Azure Marketplace i AppSource Podręcznik publikowania](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide) i [zarządzania platformy Azure i usługi AppSource w portalu Marketplace oferty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers).

> [!IMPORTANT]
> Każdy plan w ofercie usług zarządzanych obejmuje **manifestu szczegóły** sekcji, w której zdefiniujesz jednostki usługi Azure Active Directory (Azure AD) w dzierżawie, które będzie mieć dostęp do zasobu delegowanego grupy i/lub subskrypcji dla Klienci, którzy zakupią tego planu. Należy wiedzieć, czy wszystkie grupy (lub użytkownika lub nazwy głównej usługi) w tym miejscu zawierających mają te same uprawnienia każdy klient korzystający z planu zakupu. Aby przypisać różnych grup, aby pracować z każdego klienta, należy opublikować oddzielny plan prywatnego, który jest dostępna wyłącznie dla każdego klienta.

## <a name="create-your-offer-in-the-cloud-partner-portal"></a>Tworzenie oferty w portalu Cloud Partner

1. Zaloguj się do [portalu dla partnerów w chmurze](https://cloudpartner.azure.com/).
2. W menu nawigacji po lewej stronie wybierz **nowa oferta**, a następnie wybierz **usług zarządzanych**.
3. Zobaczysz **edytora** sekcji oferty z czterech części do wypełnienia: **Ustawienia oferty**, **plany**, **Marketplace**, i **pomocy technicznej**. Czytaj dalej, aby uzyskać wskazówki dotyczące sposobu wykonywania tych sekcji.

## <a name="enter-offer-settings"></a>Wprowadź ustawienia oferty

W **ustawienia oferty** sekcji, podaj następujące czynności:

|Pole  |Opis  |
|---------|---------|
|**Identyfikator oferty**     | Unikatowy identyfikator oferty (w ramach swojego profilu wydawcy). Ten identyfikator może zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Należy pamiętać, że identyfikator oferty mogą być widoczne dla klientów w takich miejscach, jak w adresach URL produktu i raporty rozliczeń. Po opublikowaniu oferty nie można zmienić tę wartość.        |
|**Identyfikator wydawcy**     | Identyfikator wydawcy, która zostanie skojarzona z ofertą. Jeśli masz więcej niż jeden identyfikator wydawcy, możesz wybrać ten, którego chcesz użyć dla tej oferty.       |
|**Nazwa**     | Nazwa (maksymalnie 50 znaków), który będzie wyświetlany oferty w portalu Azure Marketplace i w witrynie Azure portal. Użyj rozpoznawalną nazwę marki, który będzie zrozumiałe dla klientów — Jeśli ta oferta jest promowanie za pośrednictwem własnej witrynie internetowej, należy użyć dokładnie takiej samej nazwie w tym miejscu.        |

Gdy wszystko będzie gotowe, wybierz opcję **Zapisz**. Teraz wszystko jest gotowe na przeniesienie do **plany** sekcji.

## <a name="create-plans"></a>Tworzenie planów

Każda oferta musi mieć co najmniej jeden plan (czasami określane jako jednostki SKU). Można dodać wiele planów obsługi zestawy różnych funkcji w różnych cenach lub dostosować określonego planu dla ograniczonej grupy odbiorców określonych odbiorców. Klienci mogą wyświetlać plany, które są dla niego dostępne w ramach oferty nadrzędne.

W sekcji planów w każdym planie, w której chcesz utworzyć, wybierz **nowy Plan**. Następnie wprowadź **Identyfikatorem planu**. Ten identyfikator może zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia, maksymalnie 50 znaków. Identyfikator planu mogą być widoczne dla klientów w takich miejscach, jak w adresach URL produktu i raporty rozliczeń. Po opublikowaniu oferty nie można zmienić tę wartość.

Następnie wykonaj następujące sekcje w **szczegóły planu** sekcji:

|Pole  |Opis  |
|---------|---------|
|**Tytuł**     | Przyjazna nazwa dla planu dla ekranu. Składać z maksymalnie 50 znaków.        |
|**Podsumowanie**     | Zwięzły opis planu dla ekranu pod tytułem. Składać z maksymalnie 100 znaków.        |
|**Opis**     | Tekst opisu, który zawiera bardziej szczegółowy opis planu.         |
|**Model rozliczania**     | Istnieją 2 modelami rozliczeń, pokazano poniżej, ale musisz wybrać **model dostarczania własnej licencji** dla oferty usług zarządzanych. Oznacza to, że rozlicza klientów bezpośrednio koszty związane z tej oferty, a Microsoft nie nalicza opłat, możesz.   |
|**Jest to prywatne planu?**     | Wskazuje, czy jednostka SKU jest prywatny lub publiczny. Wartość domyślna to **nie** (publicznego). Jeśli odejdziesz, zaznacz to pole wyboru, Twój plan nie będzie ograniczony do konkretnych klientów (lub pewną liczbę klientów); Po opublikowaniu publiczny plan możesz nie można później zmienić na prywatną. Aby udostępnić ten plan tylko do określonych odbiorców, wybierz **tak**. Jeśli tak zrobisz, należy zidentyfikować klientów, podając swój identyfikator subskrypcji. Mogą to być jeden wprowadzone przez jedną (na maksymalnie 10 subskrypcje) lub przez przekazanie pliku CSV (na maksymalnie 20 000 subskrypcje). Należy uwzględnić własne subskrypcjami tutaj, aby można było przetestować i zweryfikować oferty. Aby uzyskać więcej informacji, zobacz [prywatne jednostki SKU i planów](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus).  |

Na koniec Ukończ **manifestu szczegóły** sekcji. Spowoduje to utworzenie manifest za pomocą informacji o autoryzacji do zarządzania zasobami klienta. Informacje podane w tym miejscu jest delegowane klientów platformy Azure niezbędnych do dołączenia, zarządzania zasobami. Jak wspomniano powyżej, uprawnienia te dotyczą każdego klienta, który zakupi plan, więc jeśli chcesz ograniczyć dostęp do określonego klienta, należy opublikować prywatnej plan do ich wyłącznego użytku.

- Najpierw podaj **wersji** manifestu. Użyj formatu *n.n.n* (na przykład 1.2.5).
- Następnie wprowadź swoje **identyfikator dzierżawy**. Jest to identyfikator GUID skojarzony z Identyfikatorem dzierżawy usługi Azure Active Directory w Twojej organizacji (czyli dzierżawy, którym będziesz pracować w do zarządzania zasobami klientów). Jeśli nie masz przydatne w tym, można znaleźć ją, umieszczając kursor myszy nad swoją nazwą konta w prawym górnym po prawej stronie w witrynie Azure Portal lub wybierając **Przełącz katalog**. 
- Na koniec należy dodać co najmniej jeden **autoryzacji** wpisy dla planu. Autoryzacje Definiowanie jednostek, którzy mogą uzyskiwać dostęp do zasobów subskrypcji dla klientów, którzy zakupią planu. Aby uzyskać dostęp do zasobów w imieniu klienta przy użyciu usługi Azure resource delegowanego management, należy podać te informacje.
  Dla każdego zezwolenia należy dostarczyć następujące elementy. Następnie możesz wybrać **autoryzacji nowy** tyle razy, zgodnie z potrzebami dodać więcej definicji roli/użytkowników.
  - **Identyfikator obiektu usługi Azure AD**: Usługa Azure AD identyfikator użytkownika, grupy użytkowników lub aplikacji, która będzie pewne uprawnienia są (zgodnie z opisem w definicji roli) do zasobów Twoich klientów.
  - **Nazwa wyświetlana obiektu usługi Azure AD**: Przyjazna nazwa, aby pomóc klientom zrozumienie przeznaczenia ta autoryzacja. Odbiorcy zobaczą tę nazwę, jeśli delegowanie zasobów.
  - **Definicja roli**: Wybierz jedną z dostępnych wbudowanych ról usługi Azure AD z listy. Określają uprawnienia tej roli, użytkownikowi w **obiektów usługi Azure AD identyfikator** pola będzie miała na zasoby Twoich klientów. Aby uzyskać informacje na temat tych ról, zobacz [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  - **Role można przypisać**: W przypadku wybrania Administrator dostępu użytkowników w **definicji roli** Aby ta autoryzacja, można dodać jeden lub więcej można przypisać role w tym miejscu. Użytkownik w **obiektów usługi Azure AD identyfikator** pola będzie można przypisać te **można przypisać role** do [zarządzanych tożsamości](https://docs.microsoft.com/azure/managed-applications/publish-managed-identity). Należy pamiętać, że żadne inne uprawnienia, które zwykle są skojarzone z rolą Administrator dostępu użytkowników będzie stosować do tego użytkownika. (Jeśli nie wybrano opcji Administrator dostępu użytkowników dla definicji roli tego użytkownika, to pole nie ma znaczenia.)

> [!TIP]
> W większości przypadków należy przypisać uprawnienia do grupy użytkowników usługi Azure AD lub usługi jednostki, a nie na serię pojedynczych kont użytkowników. Dzięki temu można dodać lub usunąć dostęp dla poszczególnych użytkowników bez konieczności aktualizacji i ponownie opublikować plan, gdy zmienią się Twoje wymagania dotyczące dostępu.

Po zakończeniu dodawania plany wybierz **Zapisz**, a następnie kontynuować **Marketplace** sekcji.

## <a name="provide-marketplace-text-and-images"></a>Podaj Marketplace tekstu i obrazów

**Marketplace** sekcja jest określane tekstu i obrazów, w których klienci będą widzieć w portalu Azure Marketplace i witryny Azure portal.

Podaj informacje dotyczące następujących pól w **Przegląd** sekcji:

|Pole  |Opis  |
|---------|---------|
|**Tytuł**     |  Tytuł oferty, często długie, posiadanie nazwy. Ten tytuł będą wyróżniane w portalu marketplace. Składać z maksymalnie 50 znaków. W większości przypadków ta powinna być taka sama jak **nazwa** wprowadzony w **oferują ustawienia** sekcji.       |
|**Podsumowanie**     | Krótki opis przeznaczenia lub funkcja oferty. Zazwyczaj jest on wyświetlany pod tytułem. Składać z maksymalnie 100 znaków.        |
|**Długie podsumowanie**     | Podsumowanie dłużej przeznaczenie i funkcję oferty. Maksymalna długość 256 znaków.        |
|**Opis**     | Więcej informacji o ofercie. To pole ma maksymalną długość znaków 3000 i obsługuje prosty formatowanie HTML.        |
|**Identyfikator marketingu**     | Unikatowy identyfikator przyjaznego adresu URL. jego posłuży w portalu Marketplace w adresach URL dla tej oferty. Na przykład, jeśli Twoja nazwa wydawcy jest *contoso* i że marketingu identyfikator *Przykładowa aplikacja*, adres URL do swojej oferty w portalu Azure Marketplace będzie *https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp* .        |
|**Identyfikatory subskrypcji (wersja zapoznawcza)**     | Dodaj jeden do 100 identyfikatorów subskrypcji. Klientów skojarzonych z tych subskrypcji będzie można wyświetlić tę ofertę w portalu Azure Marketplace, zanim usługa zostanie wprowadzona na żywo. Zaleca się tym własnej subskrypcji w tym miejscu, dzięki czemu możesz wyświetlić podgląd wyświetlania oferty w portalu Azure Marketplace przed udostępnieniem jej klientów.  (Pomocy technicznej firmy Microsoft i zespoły inżynierów również będzie mogła wyświetlać oferty w taki sposób, w tym okresie wersji zapoznawczej).   |
|**Przydatne linki**     | Adresy URL powiązane z ofertą, takich jak dokumentacja, informacje o wersji, często zadawane pytania, itp.        |
|**Sugerowane kategorie (maks. 5)**     | Co najmniej jednej kategorii (maks. pięć) odnoszące się do oferty. Te kategorie umożliwiają klientom odnajdywanie oferty w portalu Azure Marketplace i witryny Azure portal.        |

W **marketingu artefaktów** sekcji, możesz przekazać logo i inne zasoby, które mają być wyświetlane z ofertą. Opcjonalnie możesz przekazać zrzuty ekranu lub łącza do filmów wideo, które mogą pomóc klientom zrozumieć swoją ofertę.

Wymagane są cztery rozmiary logo: **Mała liczba godzin (40 x 40)** , **Medium (90 x 90)** , **dużych (115 x 115)** , i **całej (255 x 155)** . Przestrzegać następujących wytycznych dla Twojego logo:

- Projekt platformy Azure ma prostą paletę kolorów. Ogranicz liczbę podstawowych i pomocniczych kolorów w logo.
- Kolory motywu portalu to biały i czarny. Nie używaj tych kolorów jako kolorów tła swojego logo. Użyj koloru, który sprawi, że logo będzie się wyróżniać w portalu. Zalecamy proste kolory podstawowe.
- Jeśli korzystasz z przezroczystym tłem, upewnij się, że logo i tekst nie są białe, black lub niebieski.
- Logo powinno być proste i bez gradientów. Nie używaj w logo tła gradientowego.
- Nie umieszczaj tekstu w logo, nawet nazwy firmy ani marki.
- Upewnij się, że logo nie jest rozciągnięte.

**Hero (815 x 290)** logo jest opcjonalne, ale zalecane. Jeśli dodasz hero logo, należy przestrzegać następujących wytycznych:

- Nie zawiera żadnego tekstu w hero logo i pamiętaj pozostawić 415 pikseli pustego miejsca po prawej stronie logo. Jest to wymagane, aby zostawić miejsce dla elementów tekstowych, które zostaną osadzone programowo: Nazwa wyświetlana wydawcy, tytuł plan oferty, długie podsumowanie.
- Tła hero logo może nie być czarny, biały lub przezroczyste. Upewnij się, że kolor tła nie jest zbyt jasny, ponieważ osadzonego tekstu pojawi się białe.
- Po opublikowaniu oferty z ikoną hero, nie można usunąć (mimo że można aktualizować ją z inną wersją w razie potrzeby).

W **prowadzić zarządzania** sekcji, możesz wybrać system CRM, w którym będzie przechowywany potencjalnych klientów, w razie potrzeby. 

Na koniec Podaj swoje **adres URL zasad zachowania poufności** i **warunki użytkowania** w **prawne** sekcji. Można również określić w tym miejscu umożliwia określenie, czy użyć [standardowej umowy](https://docs.microsoft.com/azure/marketplace/standard-contract) dla tej oferty.

Pamiętaj zapisać zmiany przed przejściem do **pomocy technicznej** sekcji.

## <a name="add-support-info"></a>Dodawanie informacji o pomocy technicznej

W **obsługuje** sekcji, podaj nazwę, adres e-mail i numer telefonu, skontaktuj się z pomocą inżynierów i klientem pomocy technicznej skontaktuj się z pomocą. Należy także podać obsługuje adresy URL. Firma Microsoft może używać tych informacji, jeśli zaistnieje taka potrzeba do skontaktowania się z Tobą dotyczące firmy oraz obsługi problemów.

Po dodaniu tych informacji, wybierz **Zapisz.**

## <a name="publish-your-offer"></a>Publikowanie oferty

Zadowala, wszystkie informacje podane, następnym krokiem po opublikować ofertę w portalu Azure Marketplace. Wybierz **Publikuj** przycisk, aby zainicjować proces polegający na wprowadzaniu oferty na żywo. Aby uzyskać więcej informacji na temat tego procesu, zobacz [oferty publikowania z portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/manage-offers/cpp-publish-offer).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [międzydzierżawowa środowiska zarządzania](../concepts/cross-tenant-management-experience.md).
- [Wyświetlanie i zarządzanie nimi klienci](view-manage-customers.md) , przechodząc do **moich klientów** w witrynie Azure portal.
