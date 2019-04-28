---
title: Jak przeprowadzić X.509 certyfikatów w usłudze Azure IoT Hub Device Provisioning Service | Dokumentacja firmy Microsoft
description: Jak wdrożyć certyfikaty X.509 z wystąpieniem usługi Device Provisioning
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 8cf5f262a758efe08ad73e2d8066ad4b736e76d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627042"
---
# <a name="how-to-roll-x509-device-certificates"></a>Jak wdrożyć certyfikaty urządzeń X.509

Podczas cyklu życia rozwiązania IoT należy wdrożyć certyfikaty. Dwa z głównych powodów stopniowe certyfikatów będzie naruszenia zabezpieczeń i wygaśnięcia certyfikatu. 

Stopniowe certyfikatów jest najlepszym rozwiązaniem w zakresie zabezpieczeń pomóc w zabezpieczeniu systemu w przypadku naruszenia. Jako część [założono metodologii naruszenia](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf), Microsoft funkcję specjalistów potrzebę posiadania zabezpieczeń reaktywnych procesach wraz ze środków zapobiegawczych. Stopniowe certyfikaty urządzeń należy uwzględnić te procesy zabezpieczeń. Częstotliwość, w której wdrażasz certyfikatów będzie zależeć od wymagania dotyczące zabezpieczeń rozwiązań. Klientów z wysoce poufne dane rozwiązania może codziennie, przerzucenie certyfikatu, podczas gdy inne wdrażać swoje certyfikaty co kilka lat.

Stopniowe certyfikaty urządzenia będą obejmować aktualizowanie certyfikatów przechowywanych na urządzeniu i w Centrum IoT hub. Później, urządzenie można ponownie udostępnić się z Centrum IoT przy użyciu normalnych [automatycznej aprowizacji](concepts-auto-provisioning.md) przy użyciu usługi Device Provisioning Service.


## <a name="obtain-new-certificates"></a>Uzyskaj nowe certyfikaty

Istnieje wiele sposobów w celu uzyskania nowych certyfikatów dla urządzeń IoT. Należą do nich uzyskiwanie certyfikatów z fabryki urządzenia, generowanie własne certyfikaty i posiadanie innej Zarządzanie tworzenia certyfikatów dla Ciebie. 

Certyfikaty są podpisane przez siebie w celu utworzenia łańcucha zaufania od certyfikatu głównego urzędu certyfikacji, aby [certyfikatu liścia](concepts-security.md#end-entity-leaf-certificate). Certyfikat podpisywania jest to certyfikat używany do podpisywania certyfikatu liścia na końcu łańcuch zaufania. Certyfikat podpisywania może być certyfikat głównego urzędu certyfikacji lub pośredniego certyfikatu w łańcuchu zaufania. Aby uzyskać więcej informacji, zobacz [certyfikaty X.509](concepts-security.md#x509-certificates).
 
Istnieją dwa różne sposoby, aby uzyskać certyfikat podpisywania. Pierwszy sposób, co jest zalecane dla systemów produkcyjnych, jest zakup certyfikatu podpisywania z głównego urzędu certyfikacji (CA). W ten sposób tworzy powiązanie zabezpieczeń w dół do zaufanego źródła. 

Jest to druga metoda do tworzenia własnych certyfikatów X.509 przy użyciu narzędzia, takiego jak OpenSSL. To podejście to idealne narzędzie do testowania certyfikatów X.509, ale zapewnia kilka gwarancje w zakresie zabezpieczeń. Zaleca się, że używasz tylko tego podejścia do testowania, o ile nie został przygotowany do działania jako dostawcy urzędu certyfikacji.
 

## <a name="roll-the-certificate-on-the-device"></a>Przerzucenie certyfikatu na urządzeniu

Certyfikaty na urządzeniu zawsze powinny być przechowywane w bezpiecznym miejscu, takich jak [sprzętowego modułu zabezpieczeń (HSM)](concepts-device.md#hardware-security-module). Sposób wdrażać certyfikaty urządzeń będzie zależeć od jak zostały utworzone i zainstalowane na urządzeniach w pierwszej kolejności. 

Jeśli masz certyfikatów od innych firm, musisz sprawdzić w sposób ich wdrażania w swoich certyfikatów. Ten proces może być zawarta w swojej rozmieszczenie z nimi lub może być osobną usługą, jakie oferują. 

W przypadku zarządzania certyfikatami urządzeń, musisz utworzyć potok na aktualizowanie certyfikatów. Upewnij się, że zarówno certyfikaty liścia stare i nowe mają tę samą nazwę pospolitą (CN). Dzięki tej samej nazwy Pospolitej, urządzenie można ponownie udostępnić sam bez tworzenia rekordu rejestracji duplikatów. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Przerzucenie certyfikatu w usłudze IoT hub

Certyfikat urządzenia można dodać ręcznie do usługi IoT hub. Certyfikat można też zautomatyzować przy użyciu wystąpienia usługi Device Provisioning. W tym artykule przyjęto założenie, że wystąpienie usługi Device Provisioning jest używany do obsługi automatycznego aprowizowania.

Gdy urządzenie jest początkowo dostarczanymi za pośrednictwem automatycznej aprowizacji go uruchomi telefoniczny i kontaktuje się z usługą aprowizacji. Usługa aprowizowania reaguje sprawdzanie tożsamości przed utworzeniem tożsamości urządzenia w Centrum IoT hub przy użyciu certyfikatu liścia urządzenia jako poświadczenie. Usługa aprowizowania informuje następnie urządzenia, które IoT hub jest przypisany do, a urządzenie następnie używa swojego certyfikatu liścia do uwierzytelnienia i nawiązania połączenia w Centrum IoT Hub. 

Po nowego certyfikatu liścia została przeniesiona do urządzenia, możliwość nie jest już połączenia z Centrum IoT hub, ponieważ używa nowego certyfikatu do łączenia. Usługa IoT hub rozpoznaje tylko urządzenia przy użyciu starego certyfikatu. Wynik próby połączenia urządzenia będzie błąd "Brak autoryzacji" połączenia. Aby rozwiązać ten problem, należy zaktualizować wpisu rejestracji urządzenia dla nowego certyfikatu liścia urządzenia. Następnie usługa aprowizacji można zaktualizować informacji rejestru urządzeń usługi IoT Hub w chwili, gdy urządzenie jest aprowizowany ponownie. 

Jeden z dopuszczalnych wyjątków do tej awarii połączenia będzie scenariusz, w którym został utworzony [grupy rejestracji](concepts-service.md#enrollment-group) dla urządzenia w usłudze aprowizowania. W tym przypadku jeśli główny lub pośredni certyfikaty nie są stopniowego w łańcuch zaufania certyfikatów na urządzeniu, następnie urządzenie zostanie rozpoznany Jeśli nowy certyfikat jest częścią łańcuch zaufania zdefiniowane w grupie rejestracji. W razie ten scenariusz w reakcji na naruszenie zabezpieczeń, należy co najmniej niedozwolonych certyfikaty określonego urządzenia w grupie, które są uważane za zostać naruszone. Aby uzyskać więcej informacji, zobacz [czarnej listy konkretnych urządzeń w grupie rejestracji](https://docs.microsoft.com/azure/iot-dps/how-to-revoke-device-access-portal#blacklist-specific-devices-in-an-enrollment-group).

Aktualizowanie wpisów rejestracji certyfikatów walcowane odbywa się na **Zarządzanie rejestracjami** strony. Aby uzyskać dostęp do tej strony, wykonaj następujące kroki:

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do wystąpienia usługi IoT Hub Device Provisioning Service, zawierający wpis rejestracji dla urządzenia.

2. Kliknij pozycję **Zarządzanie rejestracjami**.

    ![Zarządzanie rejestracjami](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Jak obsługiwać aktualizowanie wpisu rejestracji będzie zależeć od tego, czy używasz rejestracje indywidualne lub grupy rejestracji. Procedury zalecane różnią się także w zależności od tego, czy jesteś stopniowe certyfikatów z powodu naruszenia zabezpieczeń lub data jej wygaśnięcia certyfikatu. W następujących sekcjach opisano sposób obsługi tych aktualizacji.


## <a name="individual-enrollments-and-security-breaches"></a>Rejestracje indywidualne i naruszeń zabezpieczeń

Jeśli jest stopniowe certyfikaty w reakcji na naruszenie zabezpieczeń, należy użyć następujących podejście, które natychmiast usuwa bieżący certyfikat:

1. Kliknij przycisk **rejestracje indywidualne**i kliknij pozycję identyfikator rejestracji, na liście. 

2. Kliknij przycisk **usunięcia bieżącego certyfikatu** przycisk, a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania wpisu rejestracji. Kliknij przycisk **Zapisz** po zakończeniu.

    Te kroki należy wykonać dla certyfikatu podstawowego i pomocniczego, jeśli obie naruszenia zabezpieczeń.

    ![Zarządzanie rejestracje indywidualne](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Po których bezpieczeństwo zostało naruszone certyfikat został usunięty z usługi aprowizacji, certyfikat nadal można nawiązywać połączenia urządzenia usługi IoT hub, tak długo, jak rejestracji urządzenia, jego istnieje. Można to rozwiązać dwa sposoby: 

    Pierwszy sposób byłoby ręcznie przejdź do Centrum IoT hub, a następnie natychmiast usunąć rejestrację urządzenia skojarzonego z certyfikatem, których bezpieczeństwo zostało naruszone. Następnie po urządzenia aprowizuje ponownie przy użyciu zaktualizowany certyfikat, zostanie utworzony nowej rejestracji urządzenia.     

    ![Usuwanie rejestracji urządzeń usługi IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda byłoby użycie reprovisioning pomocy technicznej, aby ponownie udostępnić urządzenia do tej samej usługi IoT hub. To podejście można zastąpić certyfikat rejestracji urządzeń w usłudze IoT hub. Aby uzyskać więcej informacji, zobacz [sposób ponownie udostępnić urządzeń](how-to-reprovision.md).

## <a name="individual-enrollments-and-certificate-expiration"></a>Rejestracje indywidualne i wygaśnięcia certyfikatu

Jeśli masz stopniowe certyfikaty, aby obsłużyć wygaśnięcia certyfikatu, należy użyć konfiguracji certyfikatu pomocniczego w następujący sposób można zredukować przestoje w przypadku próby aprowizacji urządzeń.

Później podczas pomocniczej certyfikatu również będzie bliski wygaśnięcia i musi zostać wycofana, można też obrócić za pomocą konfiguracji podstawowej. Obracanie między głównego i dodatkowego certyfikatu w ten sposób powoduje ograniczenie przestojów występujących w przypadku próby aprowizacji urządzeń.


1. Kliknij przycisk **rejestracje indywidualne**i kliknij pozycję identyfikator rejestracji, na liście. 

2. Kliknij przycisk **certyfikatu pomocniczego** a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania wpisu rejestracji. Kliknij pozycję **Zapisz**.

    ![Zarządzanie przy użyciu certyfikatu pomocniczego rejestracje indywidualne](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Później po wygaśnięciu certyfikatu podstawowego wrócić i usunąć ten certyfikat podstawowy, klikając **usunięcia bieżącego certyfikatu** przycisku.

## <a name="enrollment-groups-and-security-breaches"></a>Grupy rejestracji i naruszeń zabezpieczeń

Aby zaktualizować rejestrację grupową w reakcji na naruszenie zabezpieczeń, należy użyć jednej z poniższych metod, które spowoduje usunięcie bieżącego głównego urzędu certyfikacji lub pośredniego certyfikatu od razu.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualizuj certyfikaty, których bezpieczeństwo zostało naruszone głównego urzędu certyfikacji

1. Kliknij przycisk **certyfikaty** kartę danego wystąpienia usługi Device Provisioning.

2. Kliknij certyfikat których bezpieczeństwo zostało naruszone, na liście, a następnie kliknij przycisk **Usuń** przycisku. Potwierdź usunięcie, wprowadzając nazwę certyfikatu, a następnie kliknij przycisk **OK**. Powtórz ten proces dla wszystkich certyfikatów ze złamanymi zabezpieczeniami.

    ![Usuwanie certyfikatu głównego urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)

3. Wykonaj czynności opisane w temacie [Konfiguruj weryfikacji certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) umożliwiające dodanie i zweryfikowanie nowe certyfikaty głównego urzędu certyfikacji.

4. Kliknij przycisk **Zarządzanie rejestracjami** danego wystąpienia usługi Device Provisioning, a następnie kliknij przycisk **grup rejestracji** listy. Kliknij nazwę grupy rejestracji na liście.

5. Kliknij przycisk **certyfikatu urzędu certyfikacji**i wybierz nowy certyfikat głównego urzędu certyfikacji. Następnie kliknij przycisk **Save** (Zapisz). 

    ![Wybierz nowy certyfikat głównego urzędu certyfikacji](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Po których bezpieczeństwo zostało naruszone certyfikat został usunięty z usługi aprowizacji, certyfikat nadal można nawiązać połączenia urządzenia do usługi IoT hub, tak długo, jak istnieje rejestracje urządzeń. Można to rozwiązać dwa sposoby: 

    Pierwszy sposób byłoby ręcznie przejdź do Centrum IoT hub, a następnie natychmiast usunąć rejestrację urządzenia skojarzonego z certyfikatem, których bezpieczeństwo zostało naruszone. Następnie po urządzenia ponownie udostępnić zaktualizowane certyfikaty, nowej rejestracji urządzenia zostaną utworzone dla każdej z nich.     

    ![Usuwanie rejestracji urządzeń usługi IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda byłoby użycie reprovisioning pomocy technicznej, aby ponownie udostępnić swoje urządzenia do tej samej usługi IoT hub. To podejście można zamienić certyfikatów rejestracji urządzeń w usłudze IoT hub. Aby uzyskać więcej informacji, zobacz [sposób ponownie udostępnić urządzeń](how-to-reprovision.md).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualizuj certyfikaty pośrednie, których bezpieczeństwo zostało naruszone

1. Kliknij przycisk **grup rejestracji**, a następnie kliknij nazwę grupy na liście. 

2. Kliknij przycisk **certyfikat pośredniego**, i **usunięcia bieżącego certyfikatu**. Kliknij ikonę folderu, aby przejść do nowego certyfikatu pośredniego do przekazania do grupy rejestracji. Kliknij przycisk **Zapisz** po zakończeniu. Te kroki należy wykonać dla obu podstawowych i pomocniczych certyfikatu, jeśli zarówno naruszenia zabezpieczeń.

    Ten nowy certyfikat pośredniego powinny być podpisane przez certyfikat zweryfikowany głównego urzędu certyfikacji, który został już dodany do usługi aprowizacji. Aby uzyskać więcej informacji, zobacz [certyfikaty X.509](concepts-security.md#x509-certificates).

    ![Zarządzanie rejestracje indywidualne](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Po których bezpieczeństwo zostało naruszone certyfikat został usunięty z usługi aprowizacji, certyfikat nadal można nawiązać połączenia urządzenia do usługi IoT hub, tak długo, jak istnieje rejestracje urządzeń. Można to rozwiązać dwa sposoby: 

    Pierwszy sposób byłoby ręcznie przejdź do Centrum IoT hub, a następnie natychmiast usunąć rejestrację urządzenia skojarzonego z certyfikatem, których bezpieczeństwo zostało naruszone. Następnie po urządzenia ponownie udostępnić zaktualizowane certyfikaty, nowej rejestracji urządzenia zostaną utworzone dla każdej z nich.     

    ![Usuwanie rejestracji urządzeń usługi IoT hub](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Druga metoda byłoby użycie reprovisioning pomocy technicznej, aby ponownie udostępnić swoje urządzenia do tej samej usługi IoT hub. To podejście można zamienić certyfikatów rejestracji urządzeń w usłudze IoT hub. Aby uzyskać więcej informacji, zobacz [sposób ponownie udostępnić urządzeń](how-to-reprovision.md).


## <a name="enrollment-groups-and-certificate-expiration"></a>Grupy rejestracji i wygaśnięcia certyfikatu

Jeśli są stopniowe certyfikaty, aby obsłużyć wygaśnięcia certyfikatu, należy użyć konfiguracji certyfikatu pomocniczego w następujący sposób zapewnienie bez przestojów w przypadku próby aprowizacji urządzeń.

Później podczas pomocniczej certyfikatu również będzie bliski wygaśnięcia i musi zostać wycofana, można też obrócić za pomocą konfiguracji podstawowej. Obracanie między głównego i dodatkowego certyfikatu w ten sposób zapewnia bez przestojów w przypadku próby aprowizacji urządzeń. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualizuj wygasające certyfikaty głównego urzędu certyfikacji

1. Wykonaj czynności opisane w temacie [Konfiguruj weryfikacji certyfikatów urzędu certyfikacji](how-to-verify-certificates.md) umożliwiające dodanie i zweryfikowanie nowe certyfikaty głównego urzędu certyfikacji.

2. Kliknij przycisk **Zarządzanie rejestracjami** danego wystąpienia usługi Device Provisioning, a następnie kliknij przycisk **grup rejestracji** listy. Kliknij nazwę grupy rejestracji na liście.

3. Kliknij przycisk **certyfikatu urzędu certyfikacji**i wybierz nowy certyfikat głównego urzędu certyfikacji w ramach **certyfikatu pomocniczego** konfiguracji. Następnie kliknij przycisk **Save** (Zapisz). 

    ![Wybierz nowy certyfikat głównego urzędu certyfikacji](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Później po wygaśnięciu certyfikatu podstawowego, kliknij przycisk **certyfikaty** kartę danego wystąpienia usługi Device Provisioning. Kliknij wygasłego certyfikatu na liście, a następnie kliknij przycisk **Usuń** przycisku. Potwierdź usunięcie, wprowadzając nazwę certyfikatu, a następnie kliknij przycisk **OK**.

    ![Usuwanie certyfikatu głównego urzędu certyfikacji](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualizacja wygasa certyfikaty pośrednie


1. Kliknij przycisk **grup rejestracji**i kliknij nazwę grupy na liście. 

2. Kliknij przycisk **certyfikatu pomocniczego** a następnie kliknij ikonę folderu, aby wybrać nowy certyfikat do przekazania wpisu rejestracji. Kliknij pozycję **Zapisz**.

    Ten nowy certyfikat pośredniego powinny być podpisane przez certyfikat zweryfikowany głównego urzędu certyfikacji, który został już dodany do usługi aprowizacji. Aby uzyskać więcej informacji, zobacz [certyfikaty X.509](concepts-security.md#x509-certificates).

   ![Zarządzanie przy użyciu certyfikatu pomocniczego rejestracje indywidualne](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Później po wygaśnięciu certyfikatu podstawowego wrócić i usunąć ten certyfikat podstawowy, klikając **usunięcia bieżącego certyfikatu** przycisku.


## <a name="reprovision-the-device"></a>Ponowne udostępnianie urządzenia

Gdy certyfikat jest wycofywany na urządzeniu i usługą Device Provisioning, urządzenie można ponownie udostępnić samego, kontaktując się z usługi Device Provisioning service. 

Jeden łatwy sposób programowania urządzeń ponownie udostępnić to program urządzenie, aby skontaktować się z usługą aprowizacji, przechodzić przez przepływ inicjowania obsługi administracyjnej, gdy urządzenie otrzyma błąd "Brak autoryzacji" z próby połączenia z usługą IoT hub.

Innym sposobem jest zarówno stare i nowe certyfikaty prawidłowy krótki nakładają się i wysyłać polecenia do urządzeń za pomocą usługi IoT hub je ponownie zarejestrować za pomocą usługi aprowizacji można zaktualizować parametry połączenia Centrum IoT Hub. Ponieważ każde urządzenie może przetworzyć polecenia inaczej, trzeba będzie program urządzeniem, aby wiedzieć, co można zrobić podczas wywoływania polecenia. Istnieje kilka sposobów polecić urządzenia za pośrednictwem usługi IoT Hub, a firma Microsoft zaleca używanie [metody bezpośrednie](../iot-hub/iot-hub-devguide-direct-methods.md) lub [zadań](../iot-hub/iot-hub-devguide-jobs.md) zainicjowanie procesu.

Po zakończeniu reprovisioning urządzenia będą mogły nawiązać połączenia z Centrum IoT przy użyciu ich nowych certyfikatów.


## <a name="blacklist-certificates"></a>Certyfikaty czarnej listy

W odpowiedzi na naruszenia zabezpieczeń może być konieczne niedozwolonych certyfikatu urządzenia. Do czarnej listy certyfikatów urządzeń, wyłącz wpis rejestracji urządzenia docelowego/certyfikatu. Aby uzyskać więcej informacji, zobacz wpisywania na czarną listę urządzeń w [Zarządzanie wyrejestrowywaniem](how-to-revoke-device-access-portal.md) artykułu.

Gdy certyfikat jest dołączony jako część wpisu rejestracji wyłączone, wszelkie próby rejestrowanie w usłudze IoT hub przy użyciu którego certyfikaty zakończy się niepowodzeniem, nawet jeśli jest włączona jako część innego wpisu rejestracji.
 



## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat certyfikatów X.509 w usłudze Device Provisioning, zobacz [zabezpieczeń](concepts-security.md) 
- Aby dowiedzieć się więcej na temat dowodu posiadania dla certyfikatów X.509 urzędu certyfikacji za pomocą usługi Azure IoT Hub Device Provisioning Service, zobacz [sposób weryfikacji certyfikatów](how-to-verify-certificates.md)
- Aby dowiedzieć się więcej o tym, jak korzystać z portalu, aby utworzyć grupę rejestracji, zobacz [Zarządzanie rejestracjami urządzeń przy użyciu witryny Azure portal](how-to-manage-enrollments.md).










