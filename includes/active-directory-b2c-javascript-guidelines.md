---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/11/2020
ms.author: mimart
ms.openlocfilehash: ef08f1adc15475cd5dd38548ed39dc57532c0ef5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185843"
---
## <a name="guidelines-for-using-javascript"></a>Wskazówki dotyczące korzystania z języka JavaScript

Postępuj zgodnie z tymi wskazówkami podczas dostosowywania interfejsu aplikacji przy użyciu języka JavaScript:

- Nie wiązuj zdarzenia `<a>` kliknięcia z elementami HTML.
- Nie należy przyjmować zależności od kodu usługi Azure AD B2C lub komentarzy.
- Nie zmieniaj kolejności ani hierarchii elementów HTML usługi Azure AD B2C. Użyj zasad usługi Azure AD B2C, aby kontrolować kolejność elementów interfejsu użytkownika.
- Z następujących względów można wywołać dowolną usługę RESTful:
    - Może być konieczne ustawienie usługi RESTful CORS, aby umożliwić wywołania HTTP po stronie klienta.
    - Upewnij się, że usługa RESTful jest bezpieczna i używa tylko protokołu HTTPS.
    - Nie używaj javascript bezpośrednio do wywoływania punktów końcowych usługi Azure AD B2C.
- Możesz osadzić javascript lub połączyć się z zewnętrznymi plikami JavaScript. Korzystając z zewnętrznego pliku JavaScript, upewnij się, że używasz bezwzględnego adresu URL, a nie względnego adresu URL.
- Struktury JavaScript:
    - Usługa Azure AD B2C używa określonej wersji jQuery. Nie dołączaj innej wersji jQuery. Użycie więcej niż jednej wersji na tej samej stronie powoduje problemy.
    - Korzystanie requirejs nie jest obsługiwane.
    - Większość platform JavaScript nie są obsługiwane przez usługę Azure AD B2C.
- Ustawienia usługi Azure AD B2C `window.SETTINGS` `window.CONTENT` można odczytać przez wywołanie , obiektów, takich jak bieżący język interfejsu użytkownika. Nie zmieniaj wartości tych obiektów.
- Aby dostosować komunikat o błędzie usługi Azure AD B2C, należy użyć lokalizacji w zasadach.
- Jeśli cokolwiek można osiągnąć za pomocą zasad, ogólnie jest to zalecany sposób.
