---
title: Uzyskiwanie dostępu do zarządzanego portalu dla deweloperów i dostosowywanie ich — usługa Azure API Management | Dokumenty firmy Microsoft
description: Dowiedz się, jak korzystać z zarządzanej wersji portalu dla deweloperów w usłudze API Management.
services: api-management
documentationcenter: API Management
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/05/2020
ms.author: apimpm
ms.openlocfilehash: af7c995c11322a538dd9e27a905f1ddbc723e8ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244097"
---
# <a name="access-and-customize-developer-portal"></a>Uzyskiwanie dostępu do portalu dla deweloperów i dostosowywanie ich

Portal dla deweloperów to automatycznie generowana, w pełni konfigurowalna witryna sieci Web z dokumentacją interfejsów API. Jest to miejsce, gdzie konsumenci interfejsu API można odnajdywać interfejsy API, dowiedzieć się, jak z nich korzystać i zażądać dostępu.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uzyskiwanie dostępu do zarządzanej wersji portalu dla deweloperów
> * Poruszanie się po interfejsie administracyjnym
> * Dostosowywanie zawartości
> * Publikowanie zmian
> * Wyświetlanie opublikowanego portalu

Więcej informacji można znaleźć w portalu dla deweloperów w [omówienie portalu dla deweloperów usługi Azure API Management](api-management-howto-developer-portal.md).

![Portal deweloperski api management — tryb administratora](media/api-management-howto-developer-portal-customize/cover.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="access-the-portal-as-an-administrator"></a>Uzyskiwanie dostępu do portalu jako administrator

Wykonaj poniższe czynności, aby uzyskać dostęp do zarządzanej wersji portalu.

1. Przejdź do wystąpienia usługi zarządzania interfejsami API w witrynie Azure portal.
1. Kliknij przycisk **Portal deweloperów** na górnym pasku nawigacyjnym. Zostanie otwarta nowa karta przeglądarki z wersją administracyjną portalu.

## <a name="understand-the-portals-administrative-interface"></a>Opis interfejsu administracyjnego portalu

### <a name="default-content"></a>Zawartość domyślna 

Jeśli uzyskujesz dostęp do portalu po raz pierwszy, domyślna zawartość zostanie automatycznie udostępniona w tle. Zawartość domyślna została zaprojektowana w celu zaprezentowania możliwości portalu i zminimalizowania liczby dostosowań potrzebnych do personalizacji portalu. Więcej informacji na temat zawartości portalu można dowiedzieć się więcej o zawartości portalu w [omówienie portalu dla deweloperów usługi Azure API Management.](api-management-howto-developer-portal.md)

### <a name="visual-editor"></a>Edytor wizualny

Zawartość portalu można dostosować za pomocą edytora wizualnego. Sekcje menu po lewej stronie umożliwiają tworzenie lub modyfikowanie stron, multimediów, układów, menu, stylów lub ustawień witryny. Elementy menu na dole umożliwiają przełączanie się między rzutniami (na przykład na urządzeniach mobilnych lub na komputerach), wyświetlanie elementów portalu widocznych dla uwierzytelnionych lub anonimowych użytkowników lub zapisywanie lub cofanie akcji.

Wiersze można dodać do strony, klikając niebieską ikonę ze znakiem plus. Widżety (na przykład tekst, obrazy lub listy interfejsów API) można dodać, naciskając szarą ikonę ze znakiem plus. Elementy na stronie można zmieniać za pomocą interakcji przeciąganie i upuszczanie. 

### <a name="layouts-and-pages"></a>Układy i strony

![Strony i układy](media/api-management-howto-developer-portal-customize/pages-layouts.png)

Układy określają sposób wyświetlania stron. Na przykład w domyślnej zawartości istnieją dwa układy — jeden dotyczy strony głównej, a drugi do wszystkich pozostałych stron.

Układ zostanie zastosowany do strony, dopasowując jej szablon adresu URL do adresu URL strony. Na przykład układ z szablonem adresu URL `/wiki/*` zostanie zastosowany do każdej strony z segmentem `/wiki/` w adresie URL: `/wiki/getting-started`, `/wiki/styles`itp.

Na powyższym obrazie zawartość należąca do układu jest oznaczona na niebiesko, a strona na czerwono. Sekcje menu są odpowiednio oznaczone.

### <a name="styling-guide"></a>Przewodnik po stylizacji

![Przewodnik po stylizacji](media/api-management-howto-developer-portal-customize/styling-guide.png)

Przewodnik po stylizacji to panel stworzony z myślą o projektantach. Pozwala nadzorować i stylizować wszystkie elementy wizualne w portalu. Styl jest hierarchiczny - wiele elementów dziedziczy właściwości z innych elementów. Na przykład elementy przycisku używają kolorów tekstu i tła. Aby zmienić kolor przycisku, należy zmienić oryginalny wariant koloru.

Aby edytować wariant, kliknij na niego i wybierz ikonę ołówka, która pojawi się na nim. Po wprowadzeniu zmian w wyskakującym oknie zamknij go.

### <a name="save-button"></a>Przycisk Save (Zapisz)

![Przycisk Save (Zapisz)](media/api-management-howto-developer-portal-customize/save-button.png)

Za każdym razem, gdy dokonujesz zmiany w portalu, musisz zapisać ją ręcznie, naciskając przycisk **Zapisz** w menu u dołu. Po zapisaniu zmian zmodyfikowana zawartość jest automatycznie przekazywane do usługi api Management.

## <a name="customize-the-portals-content"></a>Dostosowywanie zawartości portalu

Zanim udostępnisz portal odwiedzającym, spersonalizuj automatycznie wygenerowaną zawartość. Zalecane zmiany obejmują układy, style i zawartość strony głównej.

> [!NOTE]
> Ze względu na kwestie integracji nie można usunąć ani przenieść `/404` `/500`następujących `/captcha` `/change-password`stron `/config.json` `/confirm/invitation`pod `/confirm-v2/identities/basic/signup` `/confirm-v2/password`innym `/internal-status-0123456789abcdef` `/publish`adresem `/signin` `/signin-sso`URL: , , , , , , , , , , , , , , , . `/signup`

### <a name="home-page"></a>Strona główna

Domyślna strona **główna** jest wypełniona fikcyjną zawartością. Można usunąć całe sekcje z zawartością lub zachować strukturę i dostosować elementy jeden po drugim. Zastąp wygenerowany tekst i obrazy własnym i upewnij się, że łącza wskazują żądane lokalizacje.

### <a name="layouts"></a>Układy

Zastąp automatycznie wygenerowane logo na pasku nawigacyjnym własnym obrazem.

### <a name="styling"></a>Style

Chociaż nie trzeba dostosowywać żadnych stylów, można rozważyć dostosowanie poszczególnych elementów. Na przykład zmień kolor podstawowy, aby dopasować go do koloru marki.

### <a name="customization-example"></a>Przykład dostosowywania

W poniższym filmie pokazujemy, jak edytować zawartość portalu, dostosować wygląd witryny i opublikować zmiany.

> [!VIDEO https://www.youtube.com/embed/5mMtUSmfUlw]

## <a name="publish-the-portal"></a><a name="publish"> </a>Publikowanie portalu

Aby udostępnić portal i jego najnowsze zmiany odwiedzającym, musisz go opublikować.

1. Upewnij się, że zmiany zostały zapisane, klikając ikonę **Zapisz.**
1. Kliknij **pozycję Publikuj witrynę sieci Web** w sekcji **Operacje** w menu. Ta operacja może potrwać kilka minut.  
    ![Publikuj portal](media/api-management-howto-developer-portal-customize/publish-portal.png)

> [!NOTE]
> Portal musi zostać ponownie opublikowany po zmianach konfiguracji usługi api Management, takich jak przypisywanie domeny niestandardowej, aktualizowanie dostawców tożsamości, ustawianie delegowania, określanie warunków logowania i produktu i inne.

## <a name="visit-the-published-portal"></a>Odwiedź opublikowany portal

Po opublikowaniu portalu można uzyskać do niego dostęp pod tym `https://contoso-api.developer.azure-api.net`samym adresem URL co panel administracyjny, na przykład . Zobacz go w osobnej sesji przeglądarki (tryb przeglądania incognito / private browsing) jako zewnętrzny użytkownik.

## <a name="apply-the-cors-policy-on-apis"></a>Stosowanie zasad CORS w interfejsach API

Należy włączyć CORS (cross-origin resource sharing) w interfejsach API, aby umożliwić odwiedzającym portal przetestować interfejsy API za pośrednictwem wbudowanej konsoli interaktywnej. Więcej informacji można znaleźć w [tym artykule dokumentacji.](api-management-howto-developer-portal.md#cors)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o portalu dla deweloperów:

- [Omówienie portalu dla deweloperów usługi Azure API Management](api-management-howto-developer-portal.md)
