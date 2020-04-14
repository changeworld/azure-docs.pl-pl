---
title: Korzystanie z usługi Azure CDN z usługą SAS | Dokumenty firmy Microsoft
description: Usługa Azure CDN obsługuje korzystanie z usługi Shared Access Signature (SAS) w celu przyznania ograniczonego dostępu do kontenerów magazynu prywatnego.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.openlocfilehash: c2580aa4ee22996c1bf0fe5c86064a6543450071
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260177"
---
# <a name="using-azure-cdn-with-sas"></a>Korzystanie z usługi Azure CDN z usługą SAS

Po skonfigurowaniu konta magazynu dla usługi Azure Content Delivery Network (CDN) do korzystania z zawartości pamięci podręcznej, domyślnie każdy, kto zna adresy URL kontenerów magazynu, może uzyskać dostęp do przekazanych plików. Aby chronić pliki na koncie magazynu, można ustawić dostęp kontenerów magazynu z publicznych na prywatnych. Jeśli jednak to zrobisz, nikt nie będzie mógł uzyskać dostępu do twoich plików. 

Jeśli chcesz przyznać ograniczony dostęp do prywatnych kontenerów magazynu, możesz użyć funkcji sygnatury dostępu współdzielonego konta usługi Azure Storage. Sygnatura dostępu współdzielonego to identyfikator URI, który zapewnia ograniczone prawa dostępu do zasobów usługi Azure Storage bez ujawniania klucza konta. Można podać sygnatury dostępu Współdzielonego do klientów, którym nie ufasz za pomocą klucza konta magazynu, ale komu chcesz delegować dostęp do niektórych zasobów konta magazynu. Rozpowszechniając identyfikator URI podpisu dostępu udostępnionego do tych klientów, można udzielić im dostępu do zasobu przez określony czas.
 
Za pomocą sygnatury dostępu współdzielonego można zdefiniować różne parametry dostępu do obiektu blob, takie jak czas rozpoczęcia i wygaśnięcia, uprawnienia (odczyt/zapis) i zakresy adresów IP. W tym artykule opisano sposób używania sygnatury dostępu Współdzielonego w połączeniu z usługą Azure CDN. Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, w tym sposobu jego tworzenia i jego opcji [parametrów, zobacz Korzystanie z sygnatur dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="setting-up-azure-cdn-to-work-with-storage-sas"></a>Konfigurowanie usługi Azure CDN do pracy z sygnaturą dostępu współdzielonego magazynu
Następujące trzy opcje są zalecane do korzystania z usługi SAS z usługą Azure CDN. Wszystkie opcje zakładają, że utworzono już działający sygnatura dostępu Współdzielonego (zobacz wymagania wstępne). 
 
### <a name="prerequisites"></a>Wymagania wstępne
Aby rozpocząć, należy utworzyć konto magazynu, a następnie wygenerować sygnatury dostępu Współdzielonego dla zasobu. Można wygenerować dwa typy przechowywanych podpisów dostępu: sygnatury dostępu współdzielonego usługi lub sygnatury dostępu współdzielonego na koncie. Aby uzyskać więcej informacji, zobacz [Typy podpisów dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#types-of-shared-access-signatures).

Po wygenerowaniu tokenu sygnatury dostępu współdzielonego `?sv=<SAS token>` można uzyskać dostęp do pliku magazynu obiektów blob, dołączając go do adresu URL. Ten adres URL ma następujący format: 

`https://<account name>.blob.core.windows.net/<container>/<file>?sv=<SAS token>`
 
Przykład:
 ```
https://democdnstorage1.blob.core.windows.net/container1/demo.jpg?sv=2017-07-29&ss=b&srt=co&sp=r&se=2038-01-02T21:30:49Z&st=2018-01-02T13:30:49Z&spr=https&sig=QehoetQFWUEd1lhU5iOMGrHBmE727xYAbKJl5ohSiWI%3D
```

Aby uzyskać więcej informacji na temat ustawiania parametrów, zobacz [Zagadnienia dotyczące parametrów sygnatury](#sas-parameter-considerations) [dostępu współdzielonego i parametry sygnatury dostępu współdzielonego](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).

![Ustawienia sieci SAS sieci CDN](./media/cdn-sas-storage-support/cdn-sas-settings.png)

### <a name="option-1-using-sas-with-pass-through-to-blob-storage-from-azure-cdn"></a>Opcja 1: Używanie sygnatury dostępu Współdzielonego z przekazywaniem do magazynu obiektów blob z usługi Azure CDN

Ta opcja jest najprostsza i używa pojedynczego tokenu sygnatury dostępu Współdzielonego, który jest przekazywany z usługi Azure CDN do serwera pochodzenia.
 
1. Wybierz punkt końcowy, wybierz **reguły buforowania,** a następnie wybierz **pozycję Buforuj każdy unikatowy adres URL** z listy **buforowania ciągów zapytań.**

    ![Reguły buforowania sieci CDN](./media/cdn-sas-storage-support/cdn-caching-rules.png)

2. Po skonfigurowaniu sygnatury dostępu Współdzielonego na koncie magazynu należy użyć tokenu sygnatury dostępu współdzielonego z adresami URL punktu końcowego i serwera pochodzenia usługi CDN, aby uzyskać dostęp do pliku. 
   
   Wynikowy adres URL punktu końcowego sieci CDN ma następujący format:`https://<endpoint hostname>.azureedge.net/<container>/<file>?sv=<SAS token>`

   Przykład:   
   ```
   https://demoendpoint.azureedge.net/container1/demo.jpg/?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   
3. Dostosuj czas trwania pamięci podręcznej za pomocą reguł `Cache-Control` buforowania lub przez dodanie nagłówków na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje token sygnatury dostępu Współdzielonego jako zwykły ciąg zapytania, jako najlepsze rozwiązanie, które należy skonfigurować czas buforowania, który wygasa w czasie wygaśnięcia sygnatury dostępu Współdzielonego lub przed upływem czasu wygaśnięcia. W przeciwnym razie jeśli plik jest buforowany przez dłuższy czas niż sygnatury dostępu Współdzielonego jest aktywny, plik może być dostępny z serwera pochodzenia usługi Azure CDN po upływie czasu wygaśnięcia sygnatury dostępu Współdzielonego. Jeśli taka sytuacja wystąpi i chcesz, aby plik w pamięci podręcznej niedostępny, należy wykonać operację przeczyszczania na pliku, aby wyczyścić go z pamięci podręcznej. Aby uzyskać informacje dotyczące ustawiania czasu trwania pamięci podręcznej w usłudze Azure CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md).

### <a name="option-2-hidden-cdn-sas-token-using-a-rewrite-rule"></a>Opcja 2: Ukryty token CDN SAS przy użyciu reguły ponownego zapisu
 
Ta opcja jest dostępna tylko dla **usługi Azure CDN Premium z** profilów Verizon. Za pomocą tej opcji można zabezpieczyć magazyn obiektów blob na serwerze pochodzenia. Możesz użyć tej opcji, jeśli nie potrzebujesz określonych ograniczeń dostępu dla pliku, ale chcesz uniemożliwić użytkownikom bezpośredni dostęp do źródła magazynu, aby poprawić czas odciążania usługi Azure CDN. Token sygnatury dostępu Współdzielonego, który jest nieznany użytkownikowi, jest wymagany dla każdego, kto uzyskuje dostęp do plików w określonym kontenerze serwera pochodzenia. Jednak ze względu na regułę przepisywania adresu URL token sygnatury dostępu Współdzielonego nie jest wymagany w punkcie końcowym sieci CDN.
 
1. Użyj [aparatu reguł,](cdn-rules-engine.md) aby utworzyć regułę przepisywania adresu URL. Nowe przepisy mogą potrwać do 4 godzin.

   ![Przycisk Zarządzanie siecią CDN](./media/cdn-sas-storage-support/cdn-manage-btn.png)

   ![CdN rządzi przyciskiem silnika](./media/cdn-sas-storage-support/cdn-rules-engine-btn.png)

   Następująca przykładowa reguła przepisywania adresu URL używa wzorca wyrażenia regularnego z grupą przechwytywania i punktem końcowym o nazwie *sasstoragedemo:*
   
   Źródło:   
   `(container1\/.*)`
   
   Miejsce docelowe:   
   ```
   $1?sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Reguła przepisywania adresu](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![URL sieci CDN — reguła przepisywania lewego adresu URL sieci CDN — po prawej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

2. Po aktywowaniu nowej reguły każdy może uzyskać dostęp do plików w określonym kontenerze w punkcie końcowym usługi CDN, niezależnie od tego, czy używa tokenu sygnatury dostępu Współdzielonego w adresie URL. Oto format:`https://<endpoint hostname>.azureedge.net/<container>/<file>`
 
   Przykład:   
   `https://sasstoragedemo.azureedge.net/container1/demo.jpg`
       

3. Dostosuj czas trwania pamięci podręcznej za pomocą reguł `Cache-Control` buforowania lub przez dodanie nagłówków na serwerze źródłowym. Ponieważ usługa Azure CDN traktuje token sygnatury dostępu Współdzielonego jako zwykły ciąg zapytania, jako najlepsze rozwiązanie, które należy skonfigurować czas buforowania, który wygasa w czasie wygaśnięcia sygnatury dostępu Współdzielonego lub przed upływem czasu wygaśnięcia. W przeciwnym razie jeśli plik jest buforowany przez dłuższy czas niż sygnatury dostępu Współdzielonego jest aktywny, plik może być dostępny z serwera pochodzenia usługi Azure CDN po upływie czasu wygaśnięcia sygnatury dostępu Współdzielonego. Jeśli taka sytuacja wystąpi i chcesz, aby plik w pamięci podręcznej niedostępny, należy wykonać operację przeczyszczania na pliku, aby wyczyścić go z pamięci podręcznej. Aby uzyskać informacje dotyczące ustawiania czasu trwania pamięci podręcznej w usłudze Azure CDN, zobacz [Kontrolowanie zachowania buforowania usługi Azure CDN za pomocą reguł buforowania](cdn-caching-rules.md).

### <a name="option-3-using-cdn-security-token-authentication-with-a-rewrite-rule"></a>Opcja 3: Korzystanie z uwierzytelniania tokenu zabezpieczającego usługi CDN z regułą ponownego zapisu

Aby korzystać z uwierzytelniania tokenu zabezpieczeń usługi Azure CDN, musisz mieć profil **usługi Azure CDN Premium z usługi Verizon.** Ta opcja jest najbardziej bezpieczna i konfigurowalna. Dostęp klienta jest oparty na parametrach zabezpieczeń ustawionych w tokenie zabezpieczającym. Po utworzeniu i skonfigurowaniu tokenu zabezpieczającego będzie on wymagany we wszystkich adresach URL punktów końcowych sieci CDN. Jednak ze względu na regułę przepisywania adresu URL token sygnatury dostępu Współdzielonego nie jest wymagany w punkcie końcowym sieci CDN. Jeśli token sygnatury dostępu Współdzielonego później stanie się nieprawidłowy, usługa Azure CDN nie będzie już mogła ponownie ocenić zawartości z serwera pochodzenia.

1. [Utwórz token zabezpieczający usługi Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-token-auth#setting-up-token-authentication) i aktywuj go przy użyciu aparatu reguł dla punktu końcowego i ścieżki sieci CDN, w której użytkownicy mogą uzyskiwać dostęp do pliku.

   Adres URL punktu końcowego tokenu zabezpieczającego ma następujący format:   
   `https://<endpoint hostname>.azureedge.net/<container>/<file>?<security_token>`
 
   Przykład:   
   ```
   https://sasstoragedemo.azureedge.net/container1/demo.jpg?a4fbc3710fd3449a7c99986bkquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
       
   Opcje parametrów uwierzytelniania tokenu zabezpieczającego różnią się od opcji parametrów tokenu sygnatury dostępu Współdzielonego. Jeśli zdecydujesz się użyć czasu wygaśnięcia podczas tworzenia tokenu zabezpieczającego, należy ustawić go na taką samą wartość jak czas wygaśnięcia dla tokenu sygnatury dostępu Współdzielonego. W ten sposób gwarantuje, że czas wygaśnięcia jest przewidywalny. 
 
2. Użyj [aparatu reguł,](cdn-rules-engine.md) aby utworzyć regułę przepisywania adresu URL, aby włączyć dostęp tokenu sygnatury dostępu współdzielonego do wszystkich obiektów blob w kontenerze. Nowe przepisy mogą potrwać do 4 godzin.

   Następująca przykładowa reguła przepisywania adresu URL używa wzorca wyrażenia regularnego z grupą przechwytywania i punktem końcowym o nazwie *sasstoragedemo:*
   
   Źródło:   
   `(container1\/.*)`
   
   Miejsce docelowe:   
   ```
   $1&sv=2017-07-29&ss=b&srt=c&sp=r&se=2027-12-19T17:35:58Z&st=2017-12-19T09:35:58Z&spr=https&sig=kquaXsAuCLXomN7R00b8CYM13UpDbAHcsRfGOW3Du1M%3D
   ```
   ![Reguła przepisywania adresu](./media/cdn-sas-storage-support/cdn-url-rewrite-rule.png)
   ![URL sieci CDN — reguła przepisywania lewego adresu URL sieci CDN — po prawej](./media/cdn-sas-storage-support/cdn-url-rewrite-rule-option-4.png)

3. Jeśli odnowisz sygnaturę dostępu Współdzielonego, upewnij się, że zaktualizuj regułę przepisywania adresów URL za pomocą nowego tokenu sygnatury dostępu Współdzielonego. 

## <a name="sas-parameter-considerations"></a>Zagadnienia dotyczące parametrów sygnatury dostępu Współdzielon

Ponieważ parametry sygnatury dostępu Współdzielonego nie są widoczne dla usługi Azure CDN, usługa Azure CDN nie może zmienić jej zachowania dostarczania na ich podstawie. Zdefiniowane ograniczenia parametrów mają zastosowanie tylko do żądań, które usługa Azure CDN powoduje na serwerze pochodzenia, a nie dla żądań od klienta do usługi Azure CDN. To rozróżnienie jest ważne, aby wziąć pod uwagę przy ustawianiu parametrów sygnatury dostępu Współdzielonego. Jeśli te zaawansowane funkcje są wymagane i używasz [opcji 3,](#option-3-using-cdn-security-token-authentication-with-a-rewrite-rule)należy ustawić odpowiednie ograniczenia tokenu zabezpieczającego usługi Azure CDN.

| Nazwa parametru SAS | Opis |
| --- | --- |
| Uruchamianie | Czas, w której usługa Azure CDN może rozpocząć dostęp do pliku obiektu blob. Ze względu na pochylenie zegara (gdy sygnał zegara dociera w różnym czasie dla różnych składników), wybierz czas 15 minut wcześniej, jeśli chcesz, aby zasób był natychmiast dostępny. |
| End | Czas, po którym usługa Azure CDN nie może już uzyskać dostępu do pliku obiektu blob. Wcześniej buforowane pliki w usłudze Azure CDN są nadal dostępne. Aby kontrolować czas wygaśnięcia pliku, należy ustawić odpowiedni czas wygaśnięcia tokenu zabezpieczającego usługi Azure CDN lub przeczyścić zasób. |
| Dozwolone adresy IP | Element opcjonalny. Jeśli używasz **usługi Azure CDN firmy Verizon,** możesz ustawić ten parametr na zakresy zdefiniowane w [usłudze Azure CDN z zakresów adresów IP serwera Verizon Edge](/azure/cdn/cdn-pop-list-api). Jeśli używasz **usługi Azure CDN z Akamai**, nie można ustawić parametr zakresów ADRESÓW IP, ponieważ adresy IP nie są statyczne.|
| Dozwolone protokoły | Protokół(-y) dozwolony dla żądania złożonego za pomocą sygnatury dostępu Współdzielonego konta. Zalecane jest ustawienie HTTPS.|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat sygnatury dostępu Współdzielonego, zobacz następujące artykuły:
- [Używanie sygnatury dostępu współdzielonego (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
- [Sygnatury dostępu współdzielonego, część 2: Tworzenie i używanie sygnatury dostępu Współdzielonego z magazynem obiektów Blob](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2)

Aby uzyskać więcej informacji dotyczących konfigurowania uwierzytelniania tokenów, zobacz [Zabezpieczanie zasobów sieci dostarczania zawartości platformy Azure za pomocą uwierzytelniania tokenu.](https://docs.microsoft.com/azure/cdn/cdn-token-auth)
