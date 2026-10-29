# ![Photo by Kubilay](images/indir.png) Git

## Derinlemesine Git Komut ve Felsefe Rehberi

Bu rehber, Git versiyon kontrol sisteminin yerel mantığını, çalışma alanlarını ve can alıcı viteslerini sırasıyla anlatan bir el kitapçığıdır. Öğrenirken ve çevrenize öğretirken bu sıralamayı takip edebilirsiniz.

📐 Git'in Üç Bölgesi (The Three Sections)
![alt text](<images/Ekran Alıntısı.JPG>)

1. **Working Directory (Çalışma Alanı):** VS Code'u(siz hangi editörü kullanıyorsanız onu) açıp kod yazdığınız, dosyaları kaydettiğiniz yerdir. Git buradaki değişiklikleri fark eder ama henüz sahiplenmez (Status: Modified / Untracked).
2. **Staging Area (Hazırlık Alanı / Sahne):** İşte Git'i diğer versiyon kontrol sistemlerinden ayıran sihirli yer burasıdır. `git add` dediğinizde dosyalar bu sahneye çıkar. Neden direkt kaydetmiyoruz? Çünkü o an yaptığınız 10 değişiklikten sadece 2 tanesini paketlemek (commitlemek) isteyebilirsiniz. Sahne, size seçici olma şansı tanır.
3. **Repository (Depo / Zaman Makinesi):** `git commit -m "mesaj"` dediğiniz an, sahnedeki her şeyin fotoğrafı çekilir ve Git'in veri tabanına silinmeyecek bir "versiyon" olarak işlenir.

---

### ⚙️ Git Kurulumu ve Kimlik Yapılandırması

Git'i bilgisayarınıza kurduktan sonra, zaman makinesine bu değişiklikleri **kimin** yaptığını tanıtmanız gerekir. Git bu bilgileri her commit'e kalıcı olarak işler. Eğer bu ayarı yapmazsanız, ilk commit'inizde Git size hata verecektir.

```bash
# Dünyaya adınızı tanıtın
git config --global user.name "Adınız Soyadınız"

# Git'in sizinle iletişim kuracağı e-postayı girin
git config --global user.email "epostaniz@ornek.com"

# Ayarları doğru yaptınız mı diye kontrol edin
git config --list
```

---

### 📁 Yerel Depo (Repository) Oluşturma

Boş bir klasörü Git'in takip edebileceği akıllı bir depoya dönüştürmek, yani zaman makinesini çalıştırmak için klasörün içinde terminali açıp şu sihirli komutu yazıyoruz:

```bash
git init
```

*Bu komuttan sonra klasörün içinde gizli bir `.git` klasörü oluşur. Bu klasör Git'in hafızası, yani zaman makinesinin motorudur. Bu klasörü sakın silmeyin!*

---

### 🔄 Günlük Çalışma Akışı ve Temel Komutlar

Bir dosyayı ilk kez oluşturduğunuzda Git onu önce **"Untracked" (İzlenmeyen)** olarak görür. Dosyayı takibe almak, sahneye çıkarmak ve mühürlemek için şu akışı izleriz:

- Anlık durumu ve dosyaların hangi bölgede olduğunu inceleyin:
  
  ```bash
  git status
  ```

- Belirli bir dosyayı veya klasörü staging alanına (sahneye) ekleyin:
  
  ```bash
  git add robot.py
  git add moduller/
  ```

- Sahneye çıkan tüm izlenen değişiklikleri kalıcı olarak depoya commit edin:
  
  ```bash
  git commit -m "PDF veri ayıklama modülü tamamlandı"
  ```

- Commit geçmişini tarihe göre, tek satırlık özetler halinde listeleyin (Zaman makinesinin günlük defteri):
  
  ```bash
  git log --oneline
  ```

---

### 🌿 Branch (Dal) Yönetimi (Modern Standartlar)

Branch yapısı, ana çalışan koda (main) zarar vermeden yeni çalışma denemeleri yapmanızı sağlar.

⚠️ **Önemli Not (Eski Yol vs Yeni Yol):** Eski Git versiyonlarında `git checkout` komutu hem dal değiştiriyor hem de dosya siliyordu. Bu kafa karışıklığını önlemek için modern Git dünyasında artık dal işlemleri için sadece **`git switch`** komutunu kullanıyoruz. `checkout` komutunu tarihin tozlu sayfalarına bırakabilirsiniz.

- **Yeni dal oluşturma:**
  
  ```bash
  git branch yeni_excel_robotu
  ```

- **İlgili dala geçiş yapma (Modern Yol 🛡️):**
  
  ```bash
  git switch yeni_excel_robotu
  ```

- **Dal oluşturup doğrudan o dala geçiş yapma (Kısayol):**
  
  ```bash
  git switch -c yeni_ozellik

  ```

  *(Buradaki `-c` parametresi "create" yani oluştur kelimesinden gelir, akılda kalıcıdır.)*

- **Mevcut dalları listeleme:**
  
  ```bash
  git branch
  ```

- **Çalışmanızı ana dal ile birleştirme (Merge):**
  
  ```bash
  git switch main
  git merge yeni_excel_robotu
  ```

- **İşlevi biten dalı yerelden güvenli bir şekilde silme:**
  
  ```bash
  git branch -d yeni_excel_robotu
  ```

---

### ↩️ "Geri Dönüş" (Undo/Reset/Checkout)

GitHub sadece kodlarımızı internette saklayan ve paylaşmamızı sağlayan bir vitrindir; asıl motor ve zaman makinesi Git’in kendisidir.
Git’te "Geri Dönüş" (Undo/Reset/Checkout) konusu, bu zaman makinesinin vitesleridir.
Git'te geri dönmeyi 4 farklı panik seviyesine göre ele alabiliriz.
Çünkü hatayı nerede fark ettiğimiz, hangi geri dönüş komutunu kullanacağımızı belirler.
Şimdi bu seviyeleri ve temel akışı adım adım inceleyelim.

#### Seviye 1: "Eyvah, Kaydettim Ama Henüz Commit Atmadım!" (Panik Seviyesi: Hafif)

- **Senaryo:**
  `kitap.txt` dosyasını açtık, yanlışlıkla birkaç paragrafı sildik veya saçma sapan şeyler yazıp dosyayı kaydettik. Terminalde `git status` yazınca dosya kırmızı görünüyor ama henüz `git add` veya `git commit` yapmadık. Dosyayı en son commit ettiğimiz temiz haline geri döndürmek istiyoruz.
- **Komut:**
  
  ```bash
  git restore kitap.txt
  ```

- **Arka Planda Ne Olur?**
  Git, depodaki son fotoğrafa bakar, bilgisayarımızdaki o hatalı dosyayı çöpe atar ve yerine temiz olan orijinal kopyayı saniyeler içinde geri getirir. VS Code'a baktığımızda değişikliklerin tık diye geri alındığını görürüz.

---

#### ⏪ Seviye 2 - (Soft Reset): "Dosyayı Commit Ettim, Ama Bu Commit'ten Vazgeçtim!" (Panik Seviyesi: Orta)

- **Senaryo:**
  Yanlış bir değişiklik yaptık, hızımızı alamayıp `git add .` ve `git commit -m "Hatalı bölüm"` diyerek bunu tarihe kaydettik. Sonra kafamıza dank etti: "Bu commit tamamen yanlıştı, bunu tarihten silmeliyim ama yazdığım kodlar/metinler tamamen yok olmasın, üzerinde biraz daha çalışıp düzelteyim."
- **Komut:**
  
  ```bash
  git reset --soft HEAD~1
  ```

- **Arka Planda Ne Olur?**
  `HEAD~1` komutu, "Zamanı 1 commit geriye sar" demektir. `--soft` parametresi ise çok değerlidir: Atılan son commit'i iptal eder, tarihi siler ama yazdığınız yazılara/kodlara dokunmaz. O kodları tekrar Staging Area (Hazırlık Alanı) içine, yani yeşil renge geri koyar. Siz de hatanızı düzeltip yeniden temiz bir commit atabilirsiniz.

---

#### 💥Seviye 3 - (Hard Reset): "Her Şeyi Yak, Son Commit'e Tamamen Geri Dön!" (Panik Seviyesi: Yüksek)

- **Senaryo:**
  Projede öyle büyük bir hata yaptık ki, son commit'ten sonra yazdığımız her şey çöp oldu. Dosyalar bir sürü hata veriyor ve panik halindeyiz. "Son attığım başarılı commit anında kitap nasıl idiyse oraya geri döneyim, son commit'ten sonra yaptığım tüm değişiklikler tamamen silinsin, tertemiz bir sayfa açılsın" istiyoruz.
- **Komut:**

  ```bash
  git reset --hard HEAD
  ```

- **Arka Planda Ne Olur?**
  Bu Git'in en tehlikeli komutudur. Son başarılı commit'ten sonra yaptığımız, yazdığımız, kaydettiğimiz ne varsa bilgisayardan tamamen kazır ve siler. Projeyi kelimenin tam anlamıyla son commit anındaki haline ışınlar.

---

#### Seviye 4 (Bonus): "Sadece Geçmişteki Bir Commit'e Göz Atıp Döneceğim"

- **Senaryo:**
  Kitabın 3 gün önceki hali nasıldı diye merak ettim. Onu bozmak istemiyorum, sadece o günkü metni okuyup geri döneceğim.
  1. Önce `git log --oneline` yazıp geçmiş commit'lerin kimlik kodlarına (örn: `a1b2c3d`) bakın.
  2. Sadece o ana gitmek için zamanı ayırın: `git switch --detach a1b2c3d` yazıp o hash'e ait metni okuyun.
  3. İşiniz bitince bugüne, güncel zamana geri dönün: `git switch main`

---

### Stash

Stash: Saklamak veya depolamak anlamlarını taşır.

Git stash, Git versiyon kontrol sistemi kullanılarak yapılan değişiklikleri geçici olarak kaydetmenizi sağlayan bir özelliktir. Bu, henüz tamamlanmayan bir iş üzerinde çalışırken veya bir dal üzerinde çalışırken aniden başka bir acil işle ilgilenmeniz gerektiğinde özellikle kullanışlıdır.

Ayrıca, git stash komutunu kullanırken, mevcut değişikliklerinizin yanı sıra indekslenmemiş dosyalarınız da kaydedilir. İndekslenmemiş dosyalar, dosyaların sadece bir kısmının değiştirilmesi durumunda ortaya çıkar ve git add komutu ile stagelenmemiş dosyalar olarak tanımlanırlar.

git stash komutu, birçok farklı parametrelerle kullanılabilir. Örneğin:

git stash list: Kaydedilen tüm stash'leri listeler.
stash@{0}: git stash listesi içerisindeki ilk yani en son eklenen geçici değişiklikler listesindeki kaydedilmiş çalışma dizininin (working directory) saklandığı referans adıdır.
git stash apply: En son kaydedilen stash'i geri yükler.
git stash apply stash@{n}: Belirtilen numaralı stash'i geri yükler.
git stash drop: En son kaydedilen stash'i siler.
git stash drop stash@{n}: Belirtilen numaralı stash'i siler.
Örnek kullanımı aşağıdaki gibi olabilir:

```bash
git stash
git checkout master
Switched to branch 'master'
# master branch'te başka bir iş yapılır
git stash list
stash@{0}: WIP on feat: 1234abcd Some unfinished work
git stash apply
# çalışılan dalın üzerindeki işe geri dönülür
---


### 🙈.gitignore (Görünmez Pelerin)

.gitignore dosyası, Git sürüm kontrol sisteminde nelerin takip edilmeyeceğini ve GitHub'a yüklenmeyeceğini belirten özel bir metin dosyasıdır. Projenizde yer alan ancak depoda saklanması gerekmeyen veya saklanması sakıncalı olan dosyaları Git'e bildirmek için kullanılır.

**❓ Neden .gitignore Kullanırız?**

- Güvenlik (En Önemlisi): Şifreler, API anahtarları, veri tabanı bağlantı bilgileri (örneğin .env dosyaları) gibi gizli bilgilerin yanlışlıkla GitHub'a yüklenip herkesin erişimine açılmasını engeller.

- Depo Boyutu: Gereksiz büyük klasörlerin (örneğin Node.js projelerindeki node_modules klasörü veya derlenmiş sanal ortamlar) depoyu şişirmesini önler.

- Sistem ve Editör Çöpleri: İşletim sistemlerinin veya kod editörlerinin oluşturduğu kişisel geçici dosyaları (Windows'taki Thumbs.db, macOS'taki .DS_Store, VS Code'un .vscode klasörü) projeden uzak tutar.

- Derleme (Build) Çıktıları: Kod derlendiğinde oluşan geçici veya nihai dosyaları (örneğin .exe, .dll, dist/, build/) depoya eklemeyerek çakışmaları önler.
**📝 .gitignore Söz Dizimi (Nasıl Yazılır?)**

Projenizin ana dizininde (root) uzantısı olmayan .gitignore isimli bir dosya oluşturup içine satır satır kurallar yazarsınız:

- Belirli bir dosyayı yoksaymak:
  
```bash
  secret.json
  .env
```

- Belirli bir uzantıya sahip tüm dosyaları yoksaymak:

```bash
  *.log
  *.tmp
```

- Tüm bir klasörü ve içeriğini yoksaymak:

```bash
  node_modules/
  dist/
  vendor/
```

- İstisna tanımlamak (! sembolü):
Bir kuralın dışına tek bir dosya çıkarmak istediğinizde kullanılır.

```bash
  *.log
  !important.log
```

(Tüm log dosyalarını yoksay ama important.log dosyasını takip et.)

- Yorum satırı eklemek (# sembolü):

```bash
  # Ortam değişkenleri
  .env    
```  

**💡 Sık Yapılan Hata: Dosya Zaten Takip Ediliyorsa Ne Olur?**
.gitignore dosyasına bir dosya adını yazdıktan sonra, o dosya daha önceden Git'e eklenmiş (git add ve git commit yapılmış) ise .gitignore kuralı o dosya üzerinde çalışmaz.

Eğer daha önce yanlışlıkla commit ettiğiniz bir dosyayı .gitignorea ekleyip Git takip listesinden çıkarmak isterseniz (dosyayı bilgisayarınızdan silmeden) şu komutu çalıştırmalısınız:

```bash
  git rm --cached dosya_adi    
```

Eğer bunu Klasör için yapacaksak:

```bash
  git rm -r --cached klasor_adi/    
```

Sonrasında:  
yaptığınız değişiklikleri görmek için git status durum tespiti yapıp ve ardından,  
değişiklikleri git add ile stage alanına eklemek ve ardından,  
git commit ve git push ile depo alanına göndermeniz gerekir. (git push uzak depo için)  

**Pratik İpucu: Otomatik Şablon Kullanımı**
*Farklı diller ve teknolojiler için kullanılan .gitignore dosyaları standartlaşmıştır. Örneğin bir Python projesi ile bir React projesinin yoksayacağı dosyalar farklıdır.
gitignore.io adresine giderek kullandığınız teknolojileri (Örn: Python, Node, VisualStudioCode, Windows) yazarak projenize özel hazır .gitignore içeriğini tek tıkla oluşturabilirsiniz.*

---

💡 **Altın Kural:** Git'te commit edilmemiş (ister kırmızı, ister yeşil olsun) her dosya ve değişiklik sahipsizdir. Siz evren (branch) değiştirdiğinizde, o sahipsiz dosyaları da peşinizden sürüklemiş olursunuz. Bir şeyin bir dala "ait" olması için, orada mutlaka mühürlenmesi (commit edilmesi) şarttır.
