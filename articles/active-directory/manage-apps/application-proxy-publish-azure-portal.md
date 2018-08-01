---
title: Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD | Microsoft Docs
description: Publikowanie aplikacji lokalnych do chmury przy użyciu serwera Proxy aplikacji usługi Azure AD w witrynie Azure portal.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1224642bb7e6fc0c51b3f839a78449132db5b4bb
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39364261"
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publikowanie aplikacji przy użyciu serwera proxy aplikacji usługi Azure AD

Serwer Proxy aplikacji usługi Azure Active Directory (AD) pomaga wspierać pracowników zdalnych przez publikowanie lokalnych aplikacji można uzyskać dostęp za pośrednictwem Internetu. Możesz opublikować te aplikacje za pomocą witryny Azure portal w celu zapewnienia bezpiecznego dostępu zdalnego spoza sieci.

W tym artykule przedstawiono kroki, aby opublikować aplikację w środowisku lokalnym dzięki serwerowi Proxy aplikacji. Po ukończeniu tego artykułu użytkowników będzie dostęp do aplikacji zdalnie. I wszystko będzie gotowe do skonfigurowania dodatkowych funkcji dla aplikacji, takich jak logowanie jednokrotne, spersonalizowane informacje i wymagania dotyczące zabezpieczeń.

Jeśli dopiero zaczynasz pracę z serwerem Proxy aplikacji, więcej informacji na temat tej funkcji z artykułem [jak zapewnić bezpieczny dostęp zdalny do aplikacji lokalnych](application-proxy.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przyjęto założenie, masz już zainstalowane i zarejestrowane łącznika. Jeśli nadal potrzebujesz tych czynności, zobacz [Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik](application-proxy-enable.md).

## <a name="publish-an-on-premises-app-for-remote-access"></a>Publikowanie aplikacji w środowisku lokalnym dostępu zdalnego

Wykonaj następujące kroki, aby opublikować swoje aplikacje za pomocą serwera Proxy aplikacji. Jeśli nie zostały już pobrane i skonfigurowaniu łącznika w Twojej organizacji, przejdź do strony [Rozpoczynanie pracy z usługą serwera Proxy aplikacji i zainstalować łącznik](application-proxy-enable.md) najpierw, a następnie opublikować aplikację.

> [!TIP]
> Jeśli testujesz się serwera Proxy aplikacji po raz pierwszy, wybierz aplikację, która jest skonfigurowany do uwierzytelniania opartego na hasłach. Serwer Proxy aplikacji obsługuje inne typy uwierzytelniania, ale aplikacje oparte na hasłach są łatwiej rozpocząć pracę i szybko. 

1. Zaloguj się jako administrator w [witryny Azure portal](https://portal.azure.com/).
2. Wybierz **usługi Azure Active Directory** > **aplikacje dla przedsiębiorstw** > **nową aplikację**.

  ![Dodawanie aplikacji dla przedsiębiorstw](./media/application-proxy-publish-azure-portal/add-app.png)

3. Wybierz **wszystkich**, a następnie wybierz **aplikacje lokalne**.  

  ![Dodawanie własnej aplikacji](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Podaj następujące informacje dotyczące aplikacji:

   - **Nazwa**: Nazwa aplikacji, która będzie wyświetlana na panelu dostępu i w witrynie Azure portal. 

   - **Wewnętrzny adres URL**: adres URL, którego używasz do uzyskania dostępu do aplikacji z poziomu sieci prywatnej. Możesz wprowadzić określoną ścieżkę na serwerze zaplecza, która zostanie opublikowana, podczas gdy pozostała część serwera pozostanie nieopublikowana. W ten sposób można publikować różne Lokacje na tym samym serwerze co różne aplikacje i nadać każdej z nich własnej nazwy i reguły dostępu.

     > [!TIP]
     > W przypadku publikowania ścieżki upewnij się, że zawiera ona wszystkie niezbędne obrazy, skrypty i arkusze stylów dla aplikacji. Na przykład, jeśli aplikacja znajduje się w https://yourapp/app i korzysta z obrazów znajdujących się w https://yourapp/media, a następnie należy opublikować https://yourapp/ jako ścieżka. To wewnętrzny adres URL nie musi być stroną docelową, które widzą użytkownicy. Aby uzyskać więcej informacji, zobacz [ustaw niestandardową stronę główną dla opublikowanych aplikacji](application-proxy-configure-custom-home-page.md).

   - **Zewnętrzny adres URL**: adres użytkowników zostanie umieszczona w celu dostępu do aplikacji z spoza sieci. Jeśli nie chcesz użyć domyślnej domeny serwera Proxy aplikacji, przeczytaj temat [domenami niestandardowymi na serwer Proxy aplikacji usługi Azure AD](application-proxy-configure-custom-domain.md).
   - **Wstępne uwierzytelnianie**: jak serwer Proxy aplikacji weryfikuje użytkowników przed udzieleniem im dostępu do aplikacji. 

     - Azure Active Directory: serwer proxy aplikacji przekierowuje użytkowników, aby zalogowali się w usłudze Azure AD, co umożliwia uwierzytelnienie ich uprawnień do katalogu i aplikacji. Zaleca się pozostawienie tej opcji jako domyślne, tak aby można było korzystać z funkcji zabezpieczeń usługi Azure AD, np. dostępu warunkowego i uwierzytelniania wieloskładnikowego.
     - Przekazywanie: Użytkownicy nie mają do uwierzytelniania względem usługi Azure Active Directory, aby uzyskać dostęp do aplikacji. Nadal można skonfigurować wymagania dotyczące uwierzytelniania zaplecza.
   - **Grupa łączników**: łączników przetwarzania zdalnego dostępu do aplikacji, a następnie łącznika grupuje pomagają organizować łączniki i aplikacje według regionu, sieci lub przeznaczenia. Jeśli nie masz żadnych grup łącznika jeszcze utworzony, Twoja aplikacja zostanie przypisana do **domyślne**.

>[!NOTE]
>Jeśli aplikacja używa funkcji websockets połączyć, upewnij się, że została zainstalowana wersja łącznika 1.5.612.0 lub nowszym oraz obsługa protokołu websocket i przypisane grupy łączników używa tylko te łączniki.

   ![Konfigurowanie aplikacji](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Jeśli to konieczne, należy skonfigurować dodatkowe ustawienia. W przypadku większości aplikacji należy zachować te ustawienia w ich domyślnymi stanami. 
   - **Limit czasu aplikacji zaplecza**: Ustaw tę wartość na **długie** tylko wtedy, gdy aplikacja jest powolne uwierzytelnienia i nawiązania połączenia. 
   - **Przekształć adresy URL w nagłówkach**: Zachowaj tę wartość jako **tak** aplikacji wymagane oryginalnego nagłówka hosta w żądaniu uwierzytelnienia.
   - **Przekształć adresy URL w treści aplikacji**: Zachowaj tę wartość jako **nie** chyba że zostały zapisane na stałe HTML łącza do innych aplikacji w środowisku lokalnym, a nie Użyj domen niestandardowych. Aby uzyskać więcej informacji, zobacz [Link tłumaczenie przy użyciu serwera Proxy aplikacji](application-proxy-configure-hard-coded-link-translation.md).
   
   ![Konfigurowanie aplikacji](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Wybierz pozycję **Dodaj**.


## <a name="add-a-test-user"></a>Dodaj użytkownika testowego 

Aby sprawdzić, czy aplikacja została opublikowana poprawnie, należy dodać konto użytkownika testowego. Sprawdź, czy to konto ma już uprawnienia dostępu do aplikacji z wewnątrz sieci firmowej.

1. Wróć do bloku szybki start, wybierz **przypisać użytkownika do testowania**.

  ![Przypisywanie użytkownika na potrzeby testowania](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Na użytkowników i grup bloku wybierz **Dodaj**.

  ![Dodaj użytkownika lub grupę](./media/application-proxy-publish-azure-portal/add-user.png)

3. W bloku Dodaj przypisanie wybierz **użytkowników i grup** następnie wybierz konto, które chcesz dodać. 
4. Wybierz opcję **Przypisz**.

## <a name="test-your-published-app"></a>Testowanie opublikowanej aplikacji

W przeglądarce przejdź do zewnętrznego adresu URL, skonfigurowanego w kroku publikowania. Należy wyświetlić ekranu startowego i być w stanie zalogować się przy użyciu konta testowego, należy skonfigurować.

![Testowanie opublikowanej aplikacji](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Kolejne kroki
- [Pobieranie łączników](application-proxy-enable.md) i [Tworzenie grupy łączników](application-proxy-connector-groups.md) publikowania aplikacji w oddzielnych sieciach i lokalizacji.

- [Konfigurowanie logowania jednokrotnego](application-proxy-configure-single-sign-on-password-vaulting.md) nowo opublikowanych aplikacji
