Nazwa aplikacji: CarClub 
Autor:Mikołaj Radwan

Aplikacje można odpalać za pomocą dwóch trybów(Active profiles) dev,prod.
Jeśli odpalimy za pomocą dev będziemy wtedy działać na bazie H2.Są na niej stworzone 2 konta
login: admin@example.com Hasło:adminpass i Login:user@example.com hasło:userpass.
 
Jeśli odpalimy za pomocą prod będziemy wtedy połączeni z baza Mysql.
 
Stworzone mamy 4 tabele :genre,car,users,car_rating.
Application.yml:
 
Application-dev.yml:
 
Application-prod.yml:
 











Master.xml:
 
W paczce car znajdziemy takie klasy i interfejs. 
Klasa Car jest oznaczona Entity, co oznacza, że może być mapowana do tabeli w bazie danych. Kolumny w tej tabeli będą odpowiadać polom tej klasy.
klasy DTO służą głównie do ułatwiania przesyłania danych między różnymi częściami aplikacji oraz do kontroli i optymalizacji tego procesu. Pozwalają one na elastyczne zarządzanie danymi i ich przekazywanie w sposób zgodny z potrzebami aplikacji.
CarRepository, który rozszerza interfejs CrudRepository z biblioteki Spring Data. Jest to interfejs do komunikacji z bazą danych, służący do wykonywania operacji CRUD (Create, Read, Update, Delete) 
 
List<Car> findAllByPromotedIsTrue();: Metoda ta zwraca listę wszystkich samochodów oznaczonych jako promowane.
 
List<Car> findAllByGenre_NameIgnoreCase(String genre);: Metoda ta zwraca listę wszystkich samochodów, których nazwa gatunku (genre) pasuje do podanej nazwy, ignorując wielkość liter.
@Query("select c from Car c join c.ratings r group by c order by avg(r.rating) desc ") List<Car> findTopByRating(Pageable page);: Ta metoda definiuje własne zapytanie z wykorzystaniem języka JPQL (Java Persistence Query Language). Zwraca ona listę samochodów posortowaną według średniej oceny (rating) wystawionej przez użytkowników, grupując po samochodach. Wyniki są posortowane malejąco według średniej oceny. Jest też możliwość paginacji wyników dzięki parametrowi Pageable.
@Query("SELECT DISTINCT c FROM Car c") List<Car> findAllCars();: Ta metoda również używa własnego zapytania JPQL. Zwraca listę wszystkich samochodów, eliminując duplikaty przy użyciu słowa kluczowego DISTINCT.
Klasa CarDtoMapper ma metode static CarDto map(Car car) ,która zamienia obiekt z car na CarDto.
Klasa CarService jest serwisem w aplikacji, który zarządza operacjami na obiektach typu Car.
findAllPromotedCars(): Znajduje i zwraca listę wszystkich promowanych samochodów.
findCarById(Long id): Znajduje i zwraca samochód o określonym identyfikatorze.
findCarByGenreName(String genre): Znajduje i zwraca listę samochodów o określonym gatunku.
addCar(CarSaveDto carSaveDto): Dodaje nowy samochód na podstawie danych przesłanych w formie CarSaveDto.
updateCar(CarDto carDto, MultipartFile poster): Aktualizuje istniejący samochód na podstawie danych w obiekcie CarDto i przesłanego pliku plakatu.
deleteCarById(Long id): Usuwa samochód o określonym identyfikatorze, oraz odpowiadające mu oceny.
findAllCars(): Znajduje i zwraca listę wszystkich samochodów.
findTopCars(int size): Znajduje i zwraca listę najlepiej ocenianych samochodów o określonej liczbie (size).
 
 
 
W paczce genre znajdziemy takie klasy i interfejs:
 
Klasa Genre jest oznaczona Entity.
GenreRepository również rozszerza interfejs CrudRepository.
 
Metoda findByNameIgnoreCase(String name) pozwala na znalezienie gatunku po nazwie, niezależnie od wielkości liter. Wynik jest opcjonalny (Optional), co oznacza, że może być pusty, jeśli żaden gatunek nie pasuje do podanej nazwy.
Klasa GenreDtoMapper ma metode static GenreDto map(Genre genre) ,która zamienia obiekt z genre na genreDto.
GenreService jest serwisem służącym do zarządzania operacjami na gatunkach filmowych.
findGenreByName(String name): Metoda ta znajduje gatunek filmowy po nazwie, korzystając z repozytorium. Wynik jest opcjonalny (Optional), a następnie mapowany na obiekt GenreDto.
findAllGenres(): Metoda ta zwraca listę wszystkich gatunków filmowych, korzystając z repozytorium. Wyniki są mapowane na listę obiektów GenreDto.
addGenre(GenreDto genre): Metoda ta dodaje nowy gatunek filmowy na podstawie danych przekazanych w obiekcie GenreDto. Nowy gatunek jest tworzony na podstawie tych danych, a następnie zapisywany w bazie danych.
@Transactional: Adnotacja ta oznacza, że metoda addGenre() jest wykonywana w ramach pojedynczej transakcji. Transakcja zostanie zatwierdzona, jeśli metoda zakończy się powodzeniem, a w przypadku wystąpienia błędu zostanie wycofana. Działa to w kontekście transakcyjności baz danych, co zapewnia spójność danych i unikanie błędów związanych z operacjami na bazie danych.
 
W paczce rating znajdziemy takie klasy i interfejs.
 
Klasa Rating jest oznaczona Entity.
RatingRepository równiez rozszerza CrudRepository.
Optional<Rating> findByUser_EmailAndCar_Id(String email, Long carId): Metoda ta znajduje ocenę dla określonego użytkownika i samochodu na podstawie adresu e-mail użytkownika i identyfikatora samochodu. Wynik jest opcjonalny (Optional), co oznacza, że może nie być oceny dla danego użytkownika i samochodu.
@Transactional: Adnotacja ta oznacza, że metoda deleteCarById(Long id) jest wykonywana w ramach pojedynczej transakcji. Transakcja zostanie zatwierdzona, jeśli metoda zakończy się powodzeniem, a w przypadku wystąpienia błędu zostanie wycofana.
@Modifying: Adnotacja ta informuje Spring Data JPA, że metoda deleteCarById() będzie modyfikować dane w bazie danych poprzez wykonanie operacji usuwania.
@Query("DELETE FROM Rating c WHERE c.car.id = :id"): Jest to zapytanie JPQL (Java Persistence Query Language), które usuwa wszystkie oceny powiązane z samochodem o określonym identyfikatorze. Parametr :id jest zastępowany wartością przekazaną jako parametr metody deleteCarById().
 
addOrUpdateRating(String userEmail, long carId, int rating): Metoda ta dodaje lub aktualizuje ocenę użytkownika dla określonego samochodu. Jeśli ocena dla danego użytkownika i samochodu już istnieje, jest aktualizowana. W przeciwnym razie tworzona jest nowa ocena.
getUserRatingForCar(String userEmail, long carId): Metoda ta zwraca ocenę użytkownika dla określonego samochodu na podstawie adresu e-mail użytkownika i identyfikatora samochodu. Wynik jest opcjonalny (Optional), co oznacza, że może nie być oceny dla danego użytkownika i samochodu.
 


W paczce user znajdziemy takie klasy i interfejs.
 
Klasa User i userRole sa oznaczone Entity.
UserRepository i UserRoleRepository rozszerza CrudRepository.
W UserRepository:
 
Optional<User> findByEmail(String email): Metoda ta znajduje użytkownika po adresie e-mail. Wynik jest opcjonalny (Optional), co oznacza, że może nie być użytkownika o podanym adresie e-mail.
W UserRoleRepository:
 
Optional<UserRole> findByName(String name): Metoda ta znajduje rolę użytkownika po nazwie. Wynik jest opcjonalny (Optional), co oznacza, że może nie być roli o podanej nazwie.
UserService: 
findCredentialsByEmail(String email): Metoda ta znajduje dane uwierzytelniające użytkownika (adres e-mail i hasło) na podstawie adresu e-mail. Wynik jest opcjonalny (Optional), co oznacza, że może nie być użytkownika o podanym adresie e-mail.
registerUserWithDefaultRole(UserRegistrationDto userRegistration): Metoda ta rejestracji nowego użytkownika z domyślną rolą (w tym przypadku "USER"). Hasło użytkownika jest szyfrowane przed zapisaniem do bazy danych.
 
FileStorageService jest serwisem do obsługi przechowywania plików w aplikacji.
Konstruktor: Inicjalizuje FileStorageService z lokalizacją przechowywania plików, pobraną z konfiguracji aplikacji. Przygotowuje również katalogi przechowywania plików, tworząc je, jeśli nie istnieją.
 
prepareStorageDirectories(Path fileStoragePath, Path imageStoragePath): Metoda prywatna, która przygotowuje katalogi przechowywania plików, tworząc je, jeśli nie istnieją.
 
saveImage(MultipartFile file): Metoda publiczna, która zapisuje przesłany plik obrazu do katalogu przechowywania obrazów i zwraca nazwę zapisanego pliku.
 
saveFile(MultipartFile file): Metoda publiczna, która zapisuje przesłany plik do katalogu przechowywania plików i zwraca nazwę zapisanego pliku.
 
createFilePath(MultipartFile file, String storageLocation): Metoda prywatna, która tworzy ścieżkę do zapisu pliku, zapewniając unikalność nazwy pliku, jeśli istnieje już plik o takiej samej nazwie.
 
 
W paczce web znajdziemy takie klasy.
 
Wszystkie klasy są oznaczone Controller .
AdminController jest kontrolerem obsługującym żądania związane z panelem administracyjnym aplikacji.
 
CarManagmentController jest kontrolerem obsługującym żądania związane z zarządzaniem samochodami w panelu administracyjnym aplikacji.
Konstruktor: Inicjalizuje CarManagmentController z serwisami CarService i GenreService.
addCarForm(Model model): Metoda obsługuje żądania GET na adresie /admin/dodaj-auto, wyświetlając formularz dodawania nowego samochodu. Pobiera listę wszystkich gatunków samochodów i przekazuje je do widoku.
addCar(CarSaveDto carSaveDto, RedirectAttributes redirectAttributes): Metoda obsługuje żądania POST na adresie /admin/dodaj-auto, dodając nowy samochód na podstawie danych przesłanych przez formularz. Po dodaniu samochodu przekierowuje użytkownika z odpowiednim powiadomieniem.
editCar(Model model): Metoda obsługuje żądania GET na adresie /admin/edytuj-auto, wyświetlając listę samochodów do edycji. Pobiera listę wszystkich samochodów i przekazuje je do widoku.
editCarForm(@PathVariable long id, Model model): Metoda obsługuje żądania GET na adresie /admin/edytuj-auto/{id}, wyświetlając formularz edycji konkretnego samochodu na podstawie jego identyfikatora. Pobiera dane samochodu i listę wszystkich gatunków, przekazując je do widoku.
editCarForm(CarDto carDto, MultipartFile multipartFile, RedirectAttributes redirectAttributes): Metoda obsługuje żądania POST na adresie /admin/edytuj-auto/{id}, aktualizując dane samochodu na podstawie przesłanych danych z formularza edycji. Po aktualizacji przekierowuje użytkownika z odpowiednim powiadomieniem
deleteCarForm(Model model): Metoda obsługuje żądania GET na adresie /admin/delete-car, wyświetlając formularz do usuwania samochodów. Pobiera listę wszystkich samochodów i przekazuje je do widoku. 
deleteCarForm(@PathVariable long id, RedirectAttributes redirectAttributes): Metoda obsługuje żądania POST na adresie /admin/delete-car/{id}, usuwając samochód na podstawie jego identyfikatora. Po usunięciu przekierowuje użytkownika z odpowiednim powiadomieniem.
 
 
 
GenreManagmentController jest kontrolerem obsługującym żądania związane z zarządzaniem gatunkami filmowymi w panelu administracyjnym aplikacji.
Konstruktor: Inicjalizuje GenreManagmentController z serwisem GenreService.
addGenreForm(Model model): Metoda obsługuje żądania GET na adresie /admin/dodaj-gatunek, wyświetlając formularz dodawania nowego gatunku filmowego. Tworzy obiekt DTO gatunku i przekazuje go do widoku.
addGenre(GenreDto genre, RedirectAttributes redirectAttributes): Metoda obsługuje żądania POST na adresie /admin/dodaj-gatunek, dodając nowy gatunek filmowy na podstawie danych przesłanych przez formularz. Po dodaniu gatunku przekierowuje użytkownika z odpowiednim powiadomieniem.
 

LoginController jest kontrolerem obsługującym żądania związane z logowaniem użytkowników do aplikacji.
 
RatingController jest kontrolerem obsługującym żądania związane z ocenianiem samochodów w aplikacji. 
Konstruktor: Inicjalizuje RatingController z serwisem RatingService.
addMovieRating: Metoda addMovieRating obsługuje żądania POST na ścieżce "ocen-auto". Otrzymuje parametry carId i rating, a także nagłówek referer oraz obiekt Authentication. Pobiera adres e-mail użytkownika z obiektu Authentication, a następnie przekazuje parametry oceny do metody addOrUpdateRating serwisu RatingService. Na koniec przekierowuje użytkownika z powrotem na poprzednią stronę, korzystając z adresu zawartego w nagłówku referer.
 

RegistrationController jest kontrolerem obsługującym żądania związane z rejestracją nowych użytkowników w aplikacji. 
Konstruktor: Inicjalizuje RegistrationController z serwisem UserService.
registrationForm(Model model): Metoda registrationForm() obsługuje żądania GET na adresie /registration, wyświetlając formularz rejestracji nowego użytkownika. Tworzy obiekt DTO dla rejestracji użytkownika i przekazuje go do widoku.
registration(UserRegistrationDto userRegistrationDto): Metoda registration() obsługuje żądania POST na adresie /registration, rejestrując nowego użytkownika na podstawie danych przesłanych przez formularz. Po rejestracji przekierowuje użytkownika na stronę główną aplikacji.
 
CarController jest kontrolerem obsługującym żądania związane z wyświetlaniem informacji o samochodach w aplikacji.
Konstruktor: Inicjalizuje CarController z serwisami CarService i RatingService.
getCar(@PathVariable long id, Model model, Authentication authentication): Metoda getCar() obsługuje żądania GET na adresie /auto/{id}, wyświetlając szczegóły danego samochodu na podstawie jego identyfikatora. Pobiera informacje o samochodzie oraz ocenę użytkownika dla tego samochodu (jeśli użytkownik jest zalogowany) i przekazuje je do widoku.
findTop10(Model model): Metoda findTop10() obsługuje żądania GET na adresie /top10, wyświetlając listę 10 najlepiej ocenianych samochodów. Pobiera listę samochodów i przekazuje je do widoku, razem z odpowiednimi informacjami nagłówkowymi.
 
GenreController jest kontrolerem obsługującym żądania związane z wyświetlaniem informacji o gatunkach samochodów w aplikacji. 
Konstruktor: Inicjalizuje GenreController z serwisami CarService i GenreService.
getGenre(@PathVariable String name, Model model): Metoda getGenre() obsługuje żądania GET na adresie /gatunek/{name}, wyświetlając szczegóły danego gatunku samochodów na podstawie jego nazwy. Pobiera informacje o gatunku oraz listę samochodów należących do tego gatunku i przekazuje je do widoku.
getGenreList(Model model): Metoda getGenreList() obsługuje żądania GET na adresie /gatunki-aut, wyświetlając listę wszystkich gatunków samochodów. Pobiera listę wszystkich gatunków i przekazuje je do widoku.
 

HomeController jest kontrolerem obsługującym żądania związane z wyświetlaniem strony głównej aplikacji.
Konstruktor: Inicjalizuje HomeController z serwisem CarService.
home(Model model): Metoda home() obsługuje żądania GET na adresie /. Pobiera listę promowanych samochodów za pomocą metody findAllPromotedCars() z serwisu CarService. Następnie przekazuje te samochody do widoku wraz z odpowiednimi informacjami nagłówkowymi.
 
W paczce config znajduja sie:
 
CustomSecurityConfig to klasa konfiguracyjna Spring Security, która definiuje niestandardową konfigurację zabezpieczeń aplikacji. Oto krótki opis jej funkcji:
Konfiguracja dostępu: Metoda filterChain() definiuje reguły dostępu dla różnych ścieżek w aplikacji. Użytkownicy muszą być uwierzytelnieni (authenticated()) do oceny samochodów ("ocen-auto") oraz muszą mieć rolę ADMIN do dostępu do wszystkich ścieżek zaczynających się od /admin/**. Pozostałe żądania są zezwolone (permitAll()).
Konfiguracja logowania: Ustawienia dotyczące logowania są określone w sekcji formLogin. Określono stronę logowania (loginPage("/login")) oraz zezwolono na dostęp do niej dla wszystkich użytkowników (permitAll()).
Konfiguracja wylogowywania: Ustawienia dotyczące wylogowywania są określone w sekcji logout. Określono, że wylogowanie jest możliwe przez wywołanie ścieżki /logout/** metodą GET. Po wylogowaniu użytkownik zostanie przekierowany na stronę logowania z parametrem informującym o poprawnym wylogowaniu.
Zabezpieczenie przed atakami CSRF: Włączono zabezpieczenie przed atakami CSRF, ale ignoruje się żądania do ścieżki /h2-console/**.
Zabezpieczenie nagłówków: Określono, że dla wszystkich żądań nagłówki mają być ustawione na sameOrigin.
Dostosowanie zabezpieczeń dla zasobów statycznych: Używając WebSecurityCustomizer, skonfigurowano ignorowanie żądań dla zasobów statycznych, takich jak obrazy, skrypty i arkusze stylów.
Kodowanie hasła: Zdefiniowano bean passwordEncoder, który używa fabryki Spring PasswordEncoderFactories do utworzenia delegowanego kodera haseł.
 
 
CustomUserDetailsService jest usługą Spring Security implementującą interfejs UserDetailsService. Oto krótki opis jego funkcji:
Konstruktor: Inicjalizuje CustomUserDetailsService z serwisem UserService, który zawiera logikę biznesową związaną z użytkownikami.
loadUserByUsername(String username): Metoda ta jest wymagana przez interfejs UserDetailsService i służy do wczytywania szczegółów użytkownika na podstawie jego nazwy użytkownika (w tym przypadku adresu e-mail). Wyszukuje ona użytkownika w bazie danych na podstawie podanego adresu e-mail za pomocą metody findCredentialsByEmail() z serwisu UserService. Jeśli użytkownik zostanie znaleziony, tworzy obiekt UserDetails za pomocą metody createUserDetails(). W przeciwnym razie zgłasza wyjątek UsernameNotFoundException.
createUserDetails(UserCredentialsDto credentials): Metoda prywatna, która tworzy obiekt UserDetails na podstawie danych uwierzytelniających użytkownika przechowywanych w obiekcie UserCredentialsDto. Używa ona buildera User z Spring Security do utworzenia obiektu UserDetails z podanym adresem e-mail, hasłem i rolami użytkownika.
 




Ponadto dokumentację API utworzoną przy pomocy Swaggera można znaleźć pod linkiem :
http://localhost:8080/swagger-ui/index.html
 

