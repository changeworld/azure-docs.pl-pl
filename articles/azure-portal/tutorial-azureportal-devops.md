---
title: 'Samouczek: DevOps z portalu Azure | Dokumentacja firmy Microsoft'
description: Informacje o różnych przepływach pracy metodyki DevOps w witrynie Azure Portal.
services: azure-portal
documentationcenter: ''
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2016
ms.author: mlearned
ms.openlocfilehash: 7fb3de72e28b19f9ca30968b267bf985ae384275
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164843"
---
# <a name="tutorial-devops-with-the-azure-portal"></a>Samouczek: DevOps z portalu Azure
Platforma Azure zawiera wiele elastycznych przepływów pracy metodyki DevOps. W tym samouczku możesz dowiedzieć się, jak wykorzystać możliwości portalu Azure, tworzenie, testowanie, wdrażania, rozwiązywanie problemów z, monitorowania i zarządzanie uruchamianie aplikacji. W tym samouczku omówiono następujące kwestie:

1. Tworzenie aplikacji sieci Web i włączanie ciągłego wdrażania
2. Tworzenie i testowanie aplikacji
3. Monitorowanie aplikacji i rozwiązywanie problemów z nią związanych
4. Ogólne zadania zarządzania aplikacją

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Tworzenie aplikacji sieci Web i włączanie ciągłego wdrażania
Aplikacja sieci Web zostanie utworzona za pomocą usługi [Azure App Service](https://azure.microsoft.com/services/app-service/), która będzie używana w pozostałej części tego samouczka. Ciągłe wdrażanie będzie początkowo Włącz z repozytorium kodu źródłowego w środowisku Azure działa prawidłowo.

1. Zaloguj się do portalu Azure
2. Wybierz kolejno pozycje **App Services** &gt; **ikona Dodaj**, a następnie wprowadź nazwę, wybierz subskrypcję i utwórz nową grupę zasobów, która posłuży jako kontener dla usługi.
   
   Grupy zasobów umożliwiają zarządzanie różnych aspektów rozwiązań, takich jak rozliczenia, wdrożenia i monitorowania wszystkich jako pojedynczej grupy za pomocą [usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
   
   ![image1][image1]
3. Po chwil zostanie utworzona usługa aplikacji. Poświęć kilka minut na zapoznanie się z opcjami menu dla tej usługi, które są dostępne w portalu.
   
   ![image2][image2]    
4. Kliknij adres URL. Zwróć uwagę na to, że możesz wybierać spośród wielu dostępnych narzędzi i repozytoriów. Możesz również używać swoich ulubionych języków i platform, na przykład .NET, Java i Ruby.
   
   ![image3][image3]    
5. Dzięki witrynie Azure Portal włączenie procesu ciągłego wdrażania jest łatwe i wymaga wykonania tylko kilku prostych czynności. W portalu Azure wybierz ustawienia z ikonę usługi app service, który został utworzony.
   
   ![image4][image4]
   
   W bloku, który zostanie otwarty po prawej stronie, przewiń do sekcji publikowania.
   
   ![image5][image5]
6. Teraz musisz skonfigurować ustawienia umożliwiające włączenie ciągłego wdrażania aplikacji. Kliknij kolejno pozycje Źródło wdrożenia i Wybierz źródło. Zwróć uwagę na różne opcje dotyczące źródeł repozytorium.
   
   ![image6][image6]
7. Na przykład wybierz GitHub. Opcjonalnie wybierz repozytorium wybranych przez użytkownika i skonfiguruj poświadczenia autoryzacji.
   
   ![image7][image7]
8. Po skonfigurowaniu autoryzacji w repozytorium możesz wybrać projekt i gałąź, które chcesz wdrożyć. Poniżej znajduje się kilka fikcyjnych przykładów.
   
   ![image8][image8]
9. Po wybraniu projektu i gałęzi kliknij przycisk OK. Zaczną pojawiać się powiadomienia dotyczące wdrożenia.
   
   ![image9][image9]
10. Wróć do witryny GitHub i wyświetl element webhook, który został utworzony w celu integracji repozytorium kontroli źródła z platformą Azure. Azure portal umożliwia integrację z usługi GitHub z kilku prostych krokach.
    
    ![image10][image10]
11. Aby pokazać ciągłe wdrażanie, szybko dodamy zawartość do repozytorium. Prostym przykładem może być dodanie przykładowego pliku tekstowego do repozytorium GitHub. Z usługą App Service możesz używać platform .NET, Ruby, Python lub innych typów aplikacji. Do wybranego repozytorium możesz dodać plik tekstowy albo aplikację platformy ASP.NET MVC, Java lub Ruby.
    
    ![image11][image11]
12. Po zatwierdzeniu zmian w repozytorium w obszarze powiadomień portalu zobaczysz informacje dotyczące inicjowania nowego wdrożenia. Jeśli zmiany nie pojawią się szybko, kliknij opcję synchronizacji.
    
    ![image12][image12]
13. Jeśli spróbujesz teraz załadować stronę usługi aplikacji, może zostać wyświetlony komunikat o błędzie 403. Dzieje się tak, ponieważ nie określono typowej konfiguracji domyślnego dokumentu dla strony, takiego jak plik index.htm lub default.html. Można to szybko rozwiązać z narzędziami w portalu Azure.  W portalu Azure wybierz ustawienia &gt; ustawienia aplikacji.
    
     ![image13][image13]
14. Zostanie otwarty blok ustawień aplikacji. Wprowadź nazwę strony — „Strona_przykładowa.html” — i kliknij pozycję Zapisz. Poświęć kilka minut na zapoznanie się z innymi ustawieniami.
    
    ![image14][image14]
15. Możesz odświeżyć adres URL w przeglądarce, aby mieć pewność, że oczekiwane zmiany są widoczne. W tym przykładzie na stronie jest wyświetlany krótki tekst. Każda kolejna zmiana w repozytorium spowoduje automatyczne włączenie nowego wdrożenia.
    
    ![image15][image15]
    
    Włączanie ciągłe wdrażanie przy użyciu portalu Azure to środowisko łatwe. Możesz również tworzyć bardziej złożone potoki tworzenia wersji i korzystać z wielu innych rozwiązań, takich jak systemy automatycznej kompilacji i zarządzania wersjami, umożliwiających wdrażanie na platformie Azure istniejących systemów kontroli źródła i ciągłej integracji.

## <a name="develop-and-test-an-app"></a>Tworzenie i testowanie aplikacji
Teraz w bazie kodu zostaną wprowadzone pewne zmiany, które zostaną szybko wdrożone. Możesz także skonfiguruje niektórych wydajności testów aplikacji sieci Web.

1. W portalu Azure w okienku nawigacji wybierz usługi aplikacji, a następnie zlokalizuj usługi aplikacji.
   
   ![image16][image16]
2. Kliknij pozycję Narzędzia.
   
   ![image17][image17]
3. W obszarze Narzędzia przejdź do kategorii Tworzenie. Istnieje kilka przydatne narzędzia w tym miejscu, które umożliwiają pracę z aplikacji bez opuszczania portalu Azure. Kliknij pozycję Konsola.
   
   ![image18][image18]
4. Za pomocą okna konsoli możesz przesyłać aktywne polecenia do aplikacji. Wpisz polecenie dir i naciśnij klawisz Enter. Pamiętaj o tym, że polecenia wymagające podwyższonego poziomu uprawnień nie będą działać.
   
   ![image19][image19]
5. Wróć do kategorii Tworzenie i wybierz pozycję Visual Studio Online. Uwaga: usługa Visual Studio Online nosi teraz nazwę Visual Studio Team Services.
   
   ![image20][image20]
6. Włącz opcję edytowania aplikacji w przeglądarce.
   
   ![image21][image21]
7. Dla aplikacji zostanie zainstalowane rozszerzenie sieci Web. Za pomocą rozszerzeń można szybko i łatwo dodawać funkcje do aplikacji na platformie Azure. Poniższy zrzut ekranu przedstawia niektóre inne typy dostępnych rozszerzeń.
   
   ![image22][image22]
8. Po zainstalowaniu rozszerzenia Visual Studio Online kliknij pozycję Przejdź.
   
   ![image23][image23]
9. Zostanie otwarta karta przeglądarki zawierająca środowisko IDE tworzenia. Zwróć uwagę na to, że poniższy interfejs jest dostępny za pomocą przeglądarki Chrome.
   
   ![image24][image24]
10. Dostępnych jest kilka działań, takich jak edytowanie plików, dodawanie plików i folderów oraz pobieranie zawartości z działającej witryny. Wprowadź drobną zmianę w pliku Strona_przykładowa.html.
    
    ![image25][image25]
11. Po chwili zmiany zostaną automatycznie zapisane i będą widoczne na stronie. Warto pamiętać, że takie wprowadzanie zmian „na żywo” nie jest zalecane w środowiskach produkcyjnych. Jednak za pomocą narzędzi można bardzo łatwo wprowadzić szybkie zmiany w środowiskach tworzenia i testowania.
    
    ![image26][image26]
    
    ![image27][image27]
12. Wróć do bloku narzędzi i kliknij pozycję Test wydajnościowy w obszarze Tworzenie.
    
    ![image28][image28]
13. Musisz skonfigurować konto usług Team Services. Więcej informacji można znaleźć w artykule [Create a Team Services Account](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (Tworzenie konta usług Team Services).
14. Kliknij pozycję Nowy, aby utworzyć test wydajnościowy.
    
    ![image29][image29]
    
    Skonfiguruj odpowiednie wartości i kliknij przycisk Uruchom test u dołu okna dialogowego, aby zainicjować test wydajnościowy.
    
    ![image30][image30]
    
    ![image31][image31]
15. Po uruchomieniu testu możesz zacząć monitorować stan.
    
    ![image32][image32]
    
    Gdy test zostanie zakończony, kliknij wynik, aby wyświetlić więcej szczegółów.
    
    ![image33][image33]
16. Przebieg testowy utworzony w tym przykładzie nie jest obszerny, dlatego zestaw danych przeznaczonych do analizy jest ograniczony. Możesz jednak wyświetlić różne metryki oraz ponownie uruchomić test z poziomu tego widoku. Azure portal umożliwia tworzenie, wykonywania i analizowanie danych wydajności sieci web, testy łatwy. Na poniższych zrzutach ekranu przedstawiono dane dotyczące wydajności.
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>Monitorowanie aplikacji i rozwiązywanie problemów z nią związanych
Platforma Azure udostępnia wiele możliwości monitorowania uruchomionych aplikacji i rozwiązywania problemów z nimi związanych.

1. W portalu Azure dla aplikacji sieci Web wybierz narzędzia.
   
   ![image37][image37]
2. W obszarze Rozwiązywanie problemów są dostępne różne narzędzia służące do rozwiązywania potencjalnych problemów z uruchomioną aplikacją. Można wykonywać takie czynności, jak na przykład monitorowanie bieżącego ruchu HTTP, włączanie samonaprawiania czy wyświetlanie dzienników.
   
   ![image38][image38]
3. Wybierz pozycję Metryki witryn, aby szybko uzyskać wgląd w wybrane kody HTTP.
   
   ![image39][image39]
4. Wybierz pozycję Diagnostyka jako usługa. Wybierz typ aplikacji, a następnie wybierz przycisk Uruchom.
   
   ![image40][image40]
   
   Rozpocznie się zbieranie danych.
   
   ![image41][image41]
5. Wybranie odpowiedniego dziennika umożliwia diagnozowanie potencjalnych problemów. Jeśli włączysz rejestrowanie, zobaczysz wszystkie dostępne opcje danych, na przykład dzienniki protokołu HTTP.
   
   ![image42][image42]
   
   Po kliknięciu pliku zrzutu pamięci możesz pobrać raport analizy DebugDiag, który ułatwia znalezienie potencjalnych problemów.
   
   ![image43][image43]
6. Aby wyświetlić więcej danych, musisz włączyć dodatkowe opcje rejestrowania. W portalu Azure przejdź do aplikacji sieci Web i wybierz ustawienia.
   
   ![image44][image44]
7. Przewiń w dół do kategorii funkcji i wybierz pozycję Dzienniki diagnostyczne.
   
      ![image45][image45]
8. Zostaną wyświetlone różne opcje rejestrowania. Włącz opcję Rejestrowanie serwera sieci Web i kliknij przycisk Zapisz.
   
   ![image46][image46]
9. Wróć do obszaru narzędzi aplikacji i wybierz pozycję Diagnostyka jako usługa, a następnie kliknij przycisk Uruchom, aby ponownie uruchomić zbieranie danych.
   
   ![image47][image47]
10. Po włączeniu opcji rejestrowania HTTP są widoczne dane rejestrowane w dziennikach HTTP.
    
    ![image48][image48]
11. Po kliknięciu pliku dziennika HTML zostanie wyświetlony rozbudowany raport obsługiwany w przeglądarce, który zapewnia bardziej szczegółowy wgląd w dane.
    
    ![image49][image49]
12. Przejście do sekcji narzędzia w portalu Azure dla aplikacji. Przewiń do sekcji Narzędzia i wybierz pozycję Eksplorator procesów.
    
    ![image50][image50]
13. Eksplorator procesów pozwala wyświetlić szczegółowe informacje o uruchomionych procesach. Uwagi poniżej można przejść do procesów i nawet kill procesy z portalu Azure.
    
    ![image51][image51]
    
    ![image52][image52]
14. Wróć do bloku Ustawienia po lewej stronie. Kliknij pozycję Nowe żądanie obsługi.
    
    ![image53][image53]
15. W bloku po prawej stronie możesz wprowadzić dane kontaktowe i szczegółowe informacje o problemach, a nawet przekazać dane diagnostyczne. Azure portal umożliwia współpracę z pomocy technicznej firmy Microsoft nie zakłóca pracy.
    
    ![image54][image54]
    
    ![image55][image55]
    
    Azure portal zapewnia wydajne i znanych narzędzi środowiska Pomoc monitora i rozwiązywanie problemów z uruchomionej aplikacji. Ponadto możesz szybko podejmować odpowiednie działania i wykonywać takie zadania, jak odtwarzanie procesów, włączanie i wyłączanie zbierania różnych danych, a także integracja z profesjonalną pomocą techniczną firmy Microsoft.

## <a name="general-application-management"></a>Ogólne zarządzanie aplikacjami
Zarządzanie aplikacjami często obejmuje szeroką gamę działań, takich jak konfigurowanie strategii tworzenia kopii zapasowych, wdrażanie dostawców tożsamości i zarządzanie nimi oraz konfigurowanie kontroli dostępu opartej na rolach. Podobnie jak w przypadku innych funkcji metodyki DevOps, platforma Azure pozwala zintegrować te zadania bezpośrednio z portalem.

1. Aby zapobiec utracie danych aplikacji sieci Web, musisz skonfigurować kopie zapasowe. Przejdź do obszaru Ustawienia swojej aplikacji sieci Web.
   
   ![image56][image56]
2. W bloku po prawej stronie przewiń w dół do kategorii Funkcje.
   
    ![image57][image57]
3. Wybierz pozycję Kopie zapasowe. Z prawej strony pojawi się odpowiedni blok.
   
   ![image58][image58]
4. Kliknij pozycję Konfiguruj i wybierz konto magazynu w bloku po prawej stronie.
   
   ![image59][image59]
5. Następnie utwórz i wybierz kontener magazynu, w którym będą przechowywane kopie zapasowe. Kliknij pozycję Utwórz w dolnej części bloku i wybierz kontener.
   
   ![image60][image60]
6. Wybrany kontener możesz można skonfigurować harmonogramy, a także skonfigurować kopie zapasowe baz danych. W tym scenariuszu kliknij ikonę Zapisz.
   
    ![image61][image61]
7. Po zapisaniu wróć do bloku Kopie zapasowe po lewej stronie. Kliknij pozycję Utwórz kopię zapasową teraz, aby utworzyć kopię zapasową aplikacji.
   
    ![image62][image62]
8. Po chwili zostanie utworzona kopia zapasowa. Zwróć uwagę na opcję Przywróć teraz, widoczną na poniższym zrzucie ekranu.
   
    ![image63][image63]
9. Kliknij pozycję Przywróć teraz i zapoznaj się z opcjami wyświetlonymi w bloku po prawej stronie. W razie potrzeby możesz wybrać odpowiednią kopię zapasową i łatwo przywrócić dane do wcześniejszego stanu. Za pomocą witryny Azure Portal udało się łatwo wdrożyć prostą strategię odzyskiwania awaryjnego dla aplikacji.
   
    ![image64][image64]
10. Wróć do bloku Ustawienia po lewej stronie i wybierz pozycję Uwierzytelnianie/autoryzacja w obszarze Funkcje.
    
     ![image65][image65]
11. W bloku po prawej stronie wybierz pozycję Uwierzytelnianie usługi App Service. Zwróć uwagę na możliwość skonfigurowania wielu popularnych dostawców.
    
     ![image66][image66]
12. Wybierz dostawcę i zapoznaj się z opcjami dotyczącymi zakresu. Po wprowadzeniu identyfikatora i klucza tajnego aplikacji możesz szybko włączyć uwierzytelnianie w aplikacji za pomocą serwisu Facebook. Azure portal umożliwia użycie uwierzytelniania, gotowe rozwiązanie dla aplikacji.
    
     ![image67][image67]
13. Wróć do bloku Ustawienia i wybierz pozycję Użytkownicy w kategorii Zarządzanie zasobami.
    
     ![image68][image68]
14. W bloku po prawej stronie zapoznaj się z różnymi opcjami dodawania ról i użytkowników. Azure portal pozwala łatwo zarządzać RBAC (kontrola dostępu oparta na rolach) dla aplikacji.
    
     ![image69][image69]

## <a name="summary"></a>Podsumowanie
W tym samouczku przedstawiono niektóre możliwości udostępniane przez platformę Azure: szybkie włączanie ciągłego wdrażania aplikacji sieci Web, wykonywanie różnych działań w zakresie tworzenia i testowania aplikacji, monitorowanie uruchomionej aplikacji i rozwiązywanie problemów z nią związanych oraz zarządzanie kluczowymi strategiami, takimi jak odzyskiwanie awaryjne, zarządzanie tożsamościami i kontrola dostępu oparta na rolach. Platforma Azure umożliwia integrację tych przepływów pracy metodyki DevOps, pozwalając użytkownikom pracować wydajnie dzięki możliwości korzystania z informacji kontekstowych dotyczących wykonywanego zadania.

## <a name="next-steps"></a>Kolejne kroki
* Usługa Azure Resource Manager pełni ważną rolę w procesie włączania metodyki DevOps na platformie Azure.  Aby dowiedzieć się więcej, zobacz artykuł [Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Aby dowiedzieć się więcej o wdrażaniu w usłudze Azure App Service, zobacz artykuł [Deploy your app to Azure App Service](../app-service/app-service-deploy-local-git.md) (Wdrażanie aplikacji w usłudze Azure App Service).

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png
