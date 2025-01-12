import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.core.type.TypeReference;

import java.io.File;
import java.io.IOException;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

abstract class BaseEntity {
    private int id;
    private LocalDateTime createdDate;

    public BaseEntity(int id) {
        this.id = id;
        this.createdDate = LocalDateTime.now();
    }

    public int getId() {
        return id;
    }

    public LocalDateTime getCreatedDate() {
        return createdDate;
    }
    public abstract void BilgiGoster();
}

interface IKayit {
void kayitOlustur();
}
class Musteri extends BaseEntity implements IKayit {
    private String isim;
    private String soyisim;
    private String email;

    public Musteri(int id, String isim, String soyisim, String email) {
        super(id);
        this.isim = isim;
        this.soyisim = soyisim;
        this.email = email;
    }
    @Override
    public void BilgiGoster() {
        System.out.println(
            "[Müşteri Bilgisi] ID: " + getId() +
            " | İsim: " + isim + " " + soyisim +
            " | Email: " + email +
            " | Kayıt Tarihi: " + getCreatedDate()
        );
    }
    @Override
    public void kayitOlustur() {
        System.out.println("Müşteri kaydı oluşturuldu: " + isim + " " + soyisim);
    }

    public String getIsim() {
        return isim;
    }

    public String getSoyisim() {
        return soyisim;
    }

    public String getEmail() {
        return email;
    }
}
class Salon extends BaseEntity implements IKayit {
    private String salonAdi;
    private int kapasite;
    private List<Integer> filmIdList;
    private List<Integer> musteriIdList;

    public Salon(int id, String salonAdi, int kapasite) {
        super(id);
        this.salonAdi = salonAdi;
        this.kapasite = kapasite;
        this.filmIdList = new ArrayList<>();
        this.musteriIdList = new ArrayList<>();
    }

    @Override
    public void BilgiGoster() {
        System.out.println(
            "[Salon Bilgisi] ID: " + getId() +
            " | Adı: " + salonAdi +
            " | Kapasite: " + kapasite +
            " | Filmler: " + filmIdList +
            " | Müşteriler: " + musteriIdList +
            " | Kayıt Tarihi: " + getCreatedDate()
        );
    }

    @Override
    public void kayitOlustur() {
        System.out.println(salonAdi + " adında bir salon oluşturuldu (Kapasite: " + kapasite + ").");
    }

    public String getSalonAdi() {
        return salonAdi;
    }

    public int getKapasite() {
        return kapasite;
    }

    public List<Integer> getFilmIdList() {
        return filmIdList;
    }

    public List<Integer> getMusteriIdList() {
        return musteriIdList;
    }
    public void filmEkle(int filmId) {
        filmIdList.add(filmId);
    }
    public void musteriEkle(int musteriId) {
        musteriIdList.add(musteriId);
    }
}
class Film {
    private int id;
    private String ad;
    private int sure; 
    private String tur; 

    public Film() { }
    
    public Film(int id, String ad, int sure, String tur) {
        this.id = id;
        this.ad = ad;
        this.sure = sure;
        this.tur = tur;
    }

    public void BilgiGoster() {
        System.out.println(
            "[Film Bilgisi] ID: " + getId() +
            " | Ad: " + ad +
            " | Süre (dk): " + sure +
            " | Tür: " + tur
        );
    }

    public int getId() {
        return id;
    }

    public String getAd() {
        return ad;
    }

    public int getSure() {
        return sure;
    }

    public String getTur() {
        return tur;
    }
}
class KayitYonetimi {
    private static final String DATA_DIR = "data"; // dosyaların saklandığı klasör
    private static final String MUSTERI_JSON = DATA_DIR + File.separator + "Musteri.json";
    private static final String FILM_JSON = DATA_DIR + File.separator + "Film.json";
    private static final String SALON_JSON = DATA_DIR + File.separator + "Salon.json";

    private ObjectMapper objectMapper;

    public KayitYonetimi() {
        objectMapper = new ObjectMapper();
        File dataFolder = new File(DATA_DIR);
        if (!dataFolder.exists()) {
            dataFolder.mkdir();
        }
    }
    public void musteriKaydet(Musteri musteri) {
        try {
            List<Musteri> musteriler = musteriListele();
            musteriler.add(musteri);
            objectMapper.writerWithDefaultPrettyPrinter().writeValue(new File(MUSTERI_JSON), musteriler);
            System.out.println("Müşteri başarıyla kaydedildi. (ID: " + musteri.getId() + ")");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public List<Musteri> musteriListele() {
        List<Musteri> musteriler = new ArrayList<>();
        try {
            File file = new File(MUSTERI_JSON);
            if (file.exists()) {
                musteriler = objectMapper.readValue(file, new TypeReference<List<Musteri>>() {});
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return musteriler;
    }
    public void filmKaydet(Film film) {
        try {
            List<Film> filmler = filmListele();
            filmler.add(film);
            objectMapper.writerWithDefaultPrettyPrinter().writeValue(new File(FILM_JSON), filmler);
            System.out.println("Film başarıyla kaydedildi. (ID: " + film.getId() + ")");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public List<Film> filmListele() {
        List<Film> filmler = new ArrayList<>();
        try {
            File file = new File(FILM_JSON);
            if (file.exists()) {
                filmler = objectMapper.readValue(file, new TypeReference<List<Film>>() {});
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return filmler;
    }
    public void salonKaydet(Salon salon) {
        try {
            List<Salon> salonlar = salonListele();
            salonlar.add(salon);
            objectMapper.writerWithDefaultPrettyPrinter().writeValue(new File(SALON_JSON), salonlar);
            System.out.println("Salon başarıyla kaydedildi. (ID: " + salon.getId() + ")");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public List<Salon> salonListele() {
        List<Salon> salonlar = new ArrayList<>();
        try {
            File file = new File(SALON_JSON);
            if (file.exists()) {
                salonlar = objectMapper.readValue(file, new TypeReference<List<Salon>>() {});
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return salonlar;
    }
}
public class SinemaMusteriKayitSistemi {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        KayitYonetimi kayitYonetimi = new KayitYonetimi();

        while (true) {
            System.out.println("\n===== SİNEMA MÜŞTERİ KAYIT SİSTEMİ =====");
            System.out.println("1 - Yeni Müşteri Ekle");
            System.out.println("2 - Müşteri Listele");
            System.out.println("3 - Yeni Film Ekle");
            System.out.println("4 - Film Listele");
            System.out.println("5 - Yeni Salon Ekle");
            System.out.println("6 - Salon Listele");
            System.out.println("7 - Çıkış");
            System.out.print("Seçiminiz: ");

            int secim;
            try {
                secim = Integer.parseInt(scanner.nextLine());
            } catch (NumberFormatException e) {
                System.out.println("Geçersiz girdi! Lütfen tekrar deneyin.");
                continue;
            }

            switch (secim) {
                case 1:
                    System.out.print("Müşteri İsmi: ");
                    String isim = scanner.nextLine();
                    System.out.print("Müşteri Soyismi: ");
                    String soyisim = scanner.nextLine();
                    System.out.print("Müşteri E-mail: ");
                    String email = scanner.nextLine();

                    int yeniMusteriId = kayitYonetimi.musteriListele().size() + 1;
                    Musteri musteri = new Musteri(yeniMusteriId, isim, soyisim, email);
                    musteri.kayitOlustur();
                    kayitYonetimi.musteriKaydet(musteri);
                    break;

                case 2:
                    List<Musteri> musteriler = kayitYonetimi.musteriListele();
                    if (musteriler.isEmpty()) {
                        System.out.println("Hiç müşteri kaydı bulunmuyor.");
                    } else {
                        System.out.println("\n--- Müşteri Listesi ---");
                        for (Musteri m : musteriler) {
                            m.BilgiGoster();
                        }
                    }
                    break;

                case 3:
                    System.out.print("Film Adı: ");
                    String filmAd = scanner.nextLine();
                    System.out.print("Film Süresi (dk): ");
                    int filmSure;
                    try {
                        filmSure = Integer.parseInt(scanner.nextLine());
                    } catch (NumberFormatException e) {
                        System.out.println("Süre bir sayı olmalıdır!");
                        break;
                    }
                    System.out.print("Film Türü: ");
                    String filmTur = scanner.nextLine();
                    int yeniFilmId = kayitYonetimi.filmListele().size() + 1;
                    Film film = new Film(yeniFilmId, filmAd, filmSure, filmTur);
                    kayitYonetimi.filmKaydet(film);
                    System.out.println("Film eklendi: " + filmAd);
                    break;

                case 4:
                    List<Film> filmler = kayitYonetimi.filmListele();
                    if (filmler.isEmpty()) {
                        System.out.println("Hiç film kaydı bulunmuyor.");
                    } else {
                        System.out.println("\n--- Film Listesi ---");
                        for (Film f : filmler) {
                            f.BilgiGoster();
                        }
                    }
                    break;

                case 5:
                    System.out.print("Salon Adı: ");
                    String salonAdi = scanner.nextLine();
                    System.out.print("Salon Kapasitesi: ");
                    int kapasite;
                    try {
                        kapasite = Integer.parseInt(scanner.nextLine());
                    } catch (NumberFormatException e) {
                        System.out.println("Kapasite bir sayı olmalıdır!");
                        break;
                    }
                    int yeniSalonId = kayitYonetimi.salonListele().size() + 1;
                    Salon salon = new Salon(yeniSalonId, salonAdi, kapasite);
                    salon.kayitOlustur();
                    kayitYonetimi.salonKaydet(salon);
                    break;

                case 6:
                    List<Salon> salonlar = kayitYonetimi.salonListele();
                    if (salonlar.isEmpty()) {
                        System.out.println("Hiç salon kaydı bulunmuyor.");
                    } else {
                        System.out.println("\n--- Salon Listesi ---");
                        for (Salon s : salonlar) {
                            s.BilgiGoster();
                        }
                    }
                    break;

                case 7:
                    System.out.println("Programdan çıkılıyor...");
                    scanner.close();
                    System.exit(0);
                    break;

                default:
                    System.out.println("Geçersiz seçim! Lütfen 1-7 arasında bir değer girin.");
            }
        }
    }
}
