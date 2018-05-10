---
title: Tworzenie raportów migawki Cloud App Discovery w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Zawiera informacje o znajdowanie aplikacji i zarządzanie nimi z Cloud App Discovery, jakie są zalety i jak działa.
services: active-directory
keywords: Usługa cloud app discovery, zarządzanie aplikacjami
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/22/2017
ms.author: barbkess
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: 33ac83e04e43088f74bb46670f0571762b57e555
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Tworzenie raportów migawki Cloud App Discovery

Przed rozpoczęciem konfigurowania modułu zbierającego dzienniki automatyczne, Przekaż dziennik ręcznie i pozwól Cloud App Security go przeanalizować. Jeśli nie masz jeszcze dziennika i chcesz zobaczyć przykładowe jak powinna wyglądać dziennika, należy wykonać poniższą procedurę, aby pobrać przykładowy plik dziennika, aby zobaczyć, jakie dziennika powinien wyglądać.

## <a name="to-create-a-snapshot-report"></a>Aby utworzyć raport migawki

1. Zbierz pliki dzienników z zapory i serwera proxy serwera za pomocą którego użytkownicy w organizacji dostęp do Internetu. Zbieranie dzienników poza godzinami szczytu ruchu, które reprezentują aktywności użytkowników w organizacji.
2. Na [na pasku menu Cloud App Security](https://portal.cloudappsecurity.com), wybierz pozycję **odnajdowania**, a następnie **Tworzenie migawki raportu**.
  
  ![Utwórz nowy raport migawek](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. Wprowadź **Nazwa raportu** i **opis**.
    
  ![Nowy raport migawki](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. Wybierz **źródła danych** z którego chcesz przekazać pliki dzienników.
5. Sprawdź format dziennika, aby upewnić się, że jest sformatowany poprawnie zgodnie z próbki, które można pobrać. Kliknij przycisk **widoku i sprawdź** , a następnie kliknij przycisk **pobierania przykładowy dziennik**. Następnie porównaj dziennik z przykładu dostępnego do upewnij się, że jest ono zgodne.
  
  ![Zweryfikuj format dziennika](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > Przykładowy format FTP jest obsługiwane w migawce i automatycznego przekazywania, natomiast syslog jest obsługiwane tylko automatycznego przekazywania. Pobieranie przykładowego dziennika pobiera przykładowy dziennik FTP.
6. **Wybierz dzienników ruchu** , który chcesz przekazać. Jednocześnie można przekazać do 20 plików. Również obsługiwane są pliki skompresowane i zip.
  
7. Kliknij przycisk **Utwórz**. Po zakończeniu przekazywania, może upłynąć trochę czasu, zanim ich przeanalizowano i przeanalizowane. Jeśli tak, Cloud App Discovery wysyła wiadomość e-mail z powiadomieniem, gdy są gotowe.

8. Wybierz **Zarządzanie raportami migawki** i wybierz migawki raportu.
  
  ![Zarządzanie raportami migawki](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>Kolejne kroki

* [Rozpoczynanie pracy przy użyciu Cloud App Discovery w usłudze Azure AD](cloudappdiscovery-get-started.md)
* [Konfigurowanie przekazywania dzienników automatyczne ciągłego raportowania](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Korzystanie z niestandardowego analizatora dziennika](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
