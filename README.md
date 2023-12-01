# Resimden Yazıya 

Bu basit uygulama, kullanıcıya bir resim dosyası seçme ve bu resimdeki metni okuma imkanı sağlar. Uygulama, Tesseract OCR ve OpenCV kütüphanelerini kullanır.

## Kullanılan Kütüphaneler

- **cv2:** OpenCV (Computer Vision) kütüphanesi, görüntü işleme ve analizi için kullanılır.
- **tkinter:** Python'un standart GUI (Grafiksel Kullanıcı Arayüzü) kütüphanesidir.
- **PIL.ImageTk, PIL.Image:** Python Imaging Library (PIL), resim işleme işlemleri için kullanılır.
- **pytesseract:** Tesseract OCR'ı Python ile kullanmamıza olanak tanır.

## Kurulum

1. Tesseract OCR'ı indirip yükleyin: [Tesseract OCR İndirme Sayfası](https://github.com/tesseract-ocr/tesseract)

   Tesseract OCR'ın kurulumunu yaparken dikkat etmeniz gereken iki husus bulunmakta bunlardan birincisi programı Program Files klasörüne kayıt etmek diğeri ise Türkçe dil paketini de indirmek.

3. Python kütüphanelerini yükleyin:

    ```bash
    pip install pytesseract Pillow
    ```

    ```bash
    pip installopencv-python
    ```

## Temel Kodlar

```python
  import cv2 
  import numpy as np
  
  from PIL import Image
  import pytesseract

  pytesseract.pytesseract.tesseract_cmd="C:\\Program Files\\Tesseract-OCR\\tesseract.exe"
  
  kaynak=""
  
  def yaziOku(dosya):
      image=cv2.imread(dosya)
  
      image=cv2.cvtColor(image,cv2.COLOR_BGR2GRAY)
  
      #Resimde Kirlilik varsa onları temizliyoruz
      kernel=np.ones((1,1),np.uint8)
      image=cv2.erode(image,kernel,iterations=1)
      image=cv2.dilate(image,kernel,iterations=1)
  
      #Resmimizde ki gri tonları siyah yapıyoruz
      image=cv2.adaptiveThreshold(image,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C,cv2.THRESH_BINARY,31,2)
      cv2.imwrite(kaynak+'temizlenmis',image)
  
      sonuc=pytesseract.image_to_string(Image.open(kaynak+'temizlenmis.png'),lang='tur')
  
      return sonuc

  print(metinOku('resim.png'))


```
Bu kodlar temel olarak `resim.png` adlı dosyadaki resmin **OpenCV** ile temizlenmesi ve ardındaan **Tesseract OCR** ile temizlenmiş resimdeki yazının okunmasını sağlıyor

fakat ben bu koda bir arayüz eklemek istedim ve bunu için **TKinter** adlı kütüphaneyi kullandım

## Arayüz Oluşturma



## Kullanım

1. **Dosya Seçme:**
    - Uygulamayı başlatın.
    - "Dosya Seç" butonuna tıklayarak bir resim dosyası seçin.

2. **Metin Okuma:**
    - "Oku" butonuna tıklayarak seçilen resimdeki metni okuyun.
    - Temizlenmiş resim, orijinal resmin bulunduğu dizine "temizlenmisResim.png" olarak kaydedilir.

3. **Metni Kopyala:**
    - Metin okuma işlemi tamamlandıktan sonra, sağ tıklayarak metni kopyalayabilirsiniz.

## Önemli Bilgilendirme

- Resim dosyasının uygulama ile aynı dizinde olmasına dikkat edin.
- Dosya yolu içerisinde Türkçe karakterler varsa, UnicodeDecodeError hatası alabilirsiniz. Bu durumu önlemek için dosya yolu ile ilgili Unicode hatasını düzeltmek adına şu kodu ekleyebilirsiniz:

    ```python
    resim_yolu = resim_yolu.encode('utf-8').decode('utf-8')
    ```

## Ekran Görüntüsü

[![Uygulama Ekran Görüntüsü](screenshot.png)](screenshot.png)

## Katkıda Bulunma

Eğer projeye katkıda bulunmak istiyorsanız, lütfen forklayın ve pull request gönderin.

## Lisans

Bu proje [MIT lisansı](LICENSE) altında lisanslanmıştır.
