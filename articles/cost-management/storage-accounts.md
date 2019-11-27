---
title: Konfigurowanie kont magazynu na platformie Cloudyn na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób konfigurowania konta usługi Azure storage i zasobników magazynu AWS na platformie Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: secdec18
ms.openlocfilehash: 1ac4442aa5a7e5e4367a03d33169412d37b3f1ea
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229925"
---
# <a name="configure-storage-accounts-for-cloudyn"></a>Konfigurowanie kont magazynu na platformie Cloudyn

<!--- intent: As a Cloudyn user, I want to configure Cloudyn to use my cloud service provider storage account to store my reports. -->

Raporty usługi Cloudyn można zapisać w portalu Cloudyn, usługi Azure storage lub usług AWS magazyn zasobników. Zapisywanie raportów w portalu Cloudyn jest bezpłatne. Jednak zapisywanie raportów magazynu dostawcy usług chmury jest opcjonalna i spowoduje naliczenie dodatkowych kosztów. Ten artykuł pomoże Ci skonfigurować konto magazynu Azure i Amazon Web Services (AWS) zasobników magazynu do przechowywania raportów.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć konto usługi Azure storage lub zasobnika magazynu Amazon.

Jeśli nie masz konta usługi Azure storage, należy ją utworzyć. Aby uzyskać więcej informacji na temat tworzenia konta usługi Azure Storage, zobacz [Tworzenie konta magazynu](../storage/common/storage-quickstart-create-account.md).

Jeśli nie masz usługi AWS simple storage service (S3) zasobnik, należy ją utworzyć. Aby uzyskać więcej informacji na temat tworzenia zasobnika S3, zobacz [Tworzenie zasobnika](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html).

## <a name="configure-your-azure-storage-account"></a>Konfigurowanie konta usługi Azure storage

Możesz konfigurowania usługi Azure storage na potrzeby używania przez firmę Cloudyn jest bardzo proste. Zbieranie informacji o koncie magazynu, a następnie skopiuj je w portalu Cloudyn.

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Kliknij pozycję **wszystkie usługi**, wybierz pozycję **konta magazynu**, przewiń do konta magazynu, którego chcesz użyć, a następnie wybierz konto.
3. Na stronie konto magazynu w obszarze **Ustawienia**kliknij pozycję **klucze dostępu**.
4. Skopiuj **nazwę konta magazynu** i **Parametry połączenia** w obszarze Klucz1.  
   ![skopiować nazwy konta magazynu i parametrów połączenia](./media/storage-accounts/azure-storage-access-keys.png)  
5. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
6. Kliknij symbol koło zębate, a następnie wybierz pozycję **raporty Zarządzanie magazynem**.
7. Kliknij przycisk **Dodaj nowe +** i upewnij się, że wybrano Microsoft Azure. Wklej nazwę konta usługi Azure Storage w obszarze **Nazwa** . Wklej **Parametry połączenia** w odpowiednim obszarze. Wprowadź nazwę kontenera, a następnie kliknij przycisk **Zapisz**.  
   ![wklej nazwę konta usługi Azure Storage i parametry połączenia w polu Dodaj nowy magazyn raportów](./media/storage-accounts/azure-cloudyn-storage.png)

   Nowy wpis raport usługi Azure storage, zostanie wyświetlony na liście kont magazynu.  
    ![Nowy wpis raport usługi Azure storage, na liście](./media/storage-accounts/azure-storage-entry.png)


Raporty można zapisać teraz w usłudze Azure storage. W każdym raporcie kliknij pozycję **Akcje** , a następnie wybierz pozycję **Raport z harmonogramu**. Nazywanie raportu i następnie dodać własnego adresu URL lub użyć automatycznie utworzonego adresu URL. Wybierz pozycję **Zapisz do magazynu** , a następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="configure-an-aws-storage-bucket"></a>Konfigurowanie przedziału magazyn AWS

Cloudyn przy użyciu istniejących poświadczeń usługi AWS: użytkownika lub roli, aby zapisać raporty do zasobnika usługi. W celu przetestowania dostępu Cloudyn próbuje zapisać mały plik tekstowy do zasobnika przy użyciu nazwy pliku _check-Bucket-Permission. txt_.

Podasz roli Cloudyn lub użytkownika z uprawnieniami PutObject do zasobnika usługi. Następnie użyj istniejącego zasobnika, lub Utwórz nowy, aby zapisywać raporty. Na koniec zdecyduj, jak zarządzać klasę magazynu, Ustaw zasady cyklu życia lub usuń niepotrzebne pliki.

###  <a name="assign-permissions-to-your-aws-user-or-role"></a>Przypisz uprawnienia użytkownika usług AWS lub roli

Podczas tworzenia nowych zasad, musisz podać uprawnienia potrzebne, aby zapisać raport przedział S3.

1. Zaloguj się do konsoli AWS i wybierz pozycję **usługi**.
2. Wybierz pozycję **IAM** z listy usług.
3. Wybierz pozycję **zasady** po lewej stronie konsoli, a następnie kliknij pozycję **Utwórz zasady**.
4. Kliknij kartę **JSON** .
5. Następujące zasady umożliwia zapisywanie raportu przedział S3. Skopiuj i wklej poniższy przykład zasad do karty **JSON** . Zastąp &lt;&gt; nazwą przedziału.

   ```json
   {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Sid":  "CloudynSaveReport2S3",
        "Effect":      "Allow",
        "Action": [
          "s3:PutObject"
        ],
        "Resource": [
          "arn:aws:s3:::<bucketname>/*"
        ]
      }
    ]
   }
   ```

6. Kliknij pozycję **Przejrzyj zasady**.  
    zasady ![AWS JSON pokazujące przykładowe informacje](./media/storage-accounts/aws-policy.png)  
7. Na stronie Przegląd zasad wpisz nazwę dla zasad. Na przykład _CloudynSaveReport2S3_.
8. Kliknij pozycję **Utwórz zasady**.

### <a name="attach-the-policy-to-a-cloudyn-role-or-user-in-your-account"></a>Dołącz zasady do rozwiązania Cloudyn roli lub użytkownika w ramach Twojego konta

Aby dołączyć nowe zasady, otwórz konsolę usług AWS i edytować rolę Cloudyn lub użytkownika.

1. Zaloguj się do konsoli usługi AWS i wybierz pozycję **usługi**, a następnie wybierz pozycję **IAM** z listy usług.
2. Wybierz pozycję **role** lub **Użytkownicy** z lewej strony konsoli.

**Dla ról:**

  1. Kliknij nazwę roli Cloudyn.
  2. Na karcie **uprawnienia** kliknij pozycję **Dołącz zasady**.
  3. Wyszukaj utworzone zasady i wybierz je, a następnie kliknij pozycję **Dołącz zasady**.
    ![przykładowe zasady dołączone do roli Cloudyn](./media/storage-accounts/aws-attach-policy-role.png)

**Dla użytkowników:**

1. Wybierz użytkownika platformy Cloudyn.
2. Na karcie **uprawnienia** kliknij pozycję **Dodaj uprawnienia**.
3. W sekcji **uprawnienia przyznawania** wybierz pozycję **Dołącz istniejące zasady bezpośrednio**.
4. Wyszukaj utworzone zasady i wybierz je, a następnie kliknij przycisk **Dalej: przegląd**.
5. Na stronie Dodawanie uprawnień do nazwy roli kliknij pozycję **Dodaj uprawnienia**.  
    ![przykładowe zasady dołączone do użytkownika Cloudyn](./media/storage-accounts/aws-attach-policy-user.png)


### <a name="optional-set-permission-with-bucket-policy"></a>Opcjonalnie: Ustawianie uprawnień za pomocą zasad zasobnika

Można również ustawić uprawnienia do tworzenia raportów w Twojej przedział S3 przy użyciu zasad zasobnika. W widoku klasycznym S3:

1. Utwórz lub wybierz istniejące przedziału.
2. Wybierz kartę **uprawnienia** , a następnie kliknij pozycję **zasady zasobników**.
3. Skopiuj i wklej poniższy przykład zasad. Zastąp &lt;\_nazwa&gt; i &lt;Cloudyn\_z ARNem przedziału.&gt; Zastąp ARN roli lub użytkownik w rozwiązaniu Cloudyn.

   ```
   {
   "Id": "Policy1485775646248",
   "Version": "2012-10-17",
   "Statement": [
    {
      "Sid": "SaveReport2S3",
      "Action": [
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": "<bucket_name>/*",
      "Principal": {
        "AWS": [
          "<Cloudyn_principle>"
        ]
      }
    }
   ]
   }
   ```

4. W edytorze zasad zasobnika kliknij przycisk **Zapisz**.

### <a name="add-aws-report-storage-to-cloudyn"></a>Dodaj magazyn raportu usług AWS do rozwiązania Cloudyn

1. Otwórz portal Cloudyn z poziomu witryny Azure Portal lub przejdź na stronę https://azure.cloudyn.com i zaloguj się.
2. Kliknij symbol koło zębate, a następnie wybierz pozycję **raporty Zarządzanie magazynem**.
3. Kliknij przycisk **Dodaj nowe +** i upewnij się, że wybrano AWS.
4. Wybierz przedział konta i magazynu. Nazwa zasobnika magazynu AWS jest wypełniane automatycznie.  
    ![Przykładowe informacje przedstawione w Dodaj nowe okno przechowywania raportu](./media/storage-accounts/aws-cloudyn-storage.png)  
5. Kliknij przycisk **Zapisz** , a następnie kliknij przycisk **OK**.

    Nowy wpis magazynu raportu AWS pojawia się na liście kont magazynu.  
    ![Nowe usługi AWS raportów magazynu wpis show liście kont magazynu](./media/storage-accounts/aws-storage-entry.png)


Raporty można zapisać teraz w usłudze Azure storage. W każdym raporcie kliknij pozycję **Akcje** , a następnie wybierz pozycję **Raport z harmonogramu**. Nazywanie raportu i następnie dodać własnego adresu URL lub użyć automatycznie utworzonego adresu URL. Wybierz pozycję **Zapisz do magazynu** , a następnie wybierz konto magazynu. Wprowadź prefiks, który pobiera dołączany do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie Zapisz raport.

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [Opis Cloudyn raportów](understanding-cost-reports.md) , aby poznać podstawową strukturę i funkcje raportów Cloudyn.
