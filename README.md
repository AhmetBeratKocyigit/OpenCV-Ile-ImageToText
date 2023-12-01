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
     
         kernel=np.ones((1,1),np.uint8)
         image=cv2.erode(image,kernel,iterations=1)
         image=cv2.dilate(image,kernel,iterations=1)
     
         image=cv2.adaptiveThreshold(image,255,cv2.ADAPTIVE_THRESH_GAUSSIAN_C,cv2.THRESH_BINARY,31,2)
         cv2.imwrite(kaynak+'temizlenmis',image)
     
         sonuc=pytesseract.image_to_string(Image.open(kaynak+'temizlenmis.png'),lang='tur')
     
         return sonuc
   
     print(metinOku('resim.png'))
   
   
   ```
Bu kodlar temel olarak `resim.png` adlı dosyadaki resmin **OpenCV** ile temizlenmesi ve ardındaan **Tesseract OCR** ile temizlenmiş resimdeki yazının okunmasını sağlıyor

fakat ben bu koda bir arayüz eklemek istedim ve bunu için **TKinter** adlı kütüphaneyi kullandım

## Arayüz Oluşturma

İlk olarak basit bir arayüz oluşturdum ve kodu bu arayüze enterge ettim

![Arayüz](https://github.com/AhmetBeratKocyigit/OpenCV-Ile-ImageToText/assets/135528027/c36f00c3-27b8-400f-a6eb-d222867909ae)

## Tam Kod

   ```python
   import cv2
   import numpy as np
   from tkinter import *
   from tkinter import filedialog
   from PIL import ImageTk, Image
   import pytesseract
   from tkinter import messagebox
   
   pytesseract.pytesseract.tesseract_cmd = "C:\\Program Files\\Tesseract-OCR\\tesseract.exe"
   
   kaynak = ""
   
   pencere = Tk()
   pencere.title("Resimden Yazıya | Beta")
   pencere.geometry("960x540")
   pencere.resizable(False, False)
   
   imaj = PhotoImage(file="arkap.png")
   image1 = imaj.subsample(2, 2)
   
   width, height = image1.width(), image1.height()
   canvas = Canvas(pencere, width=width, height=height)
   canvas.place(relx=0, rely=0, relwidth=1, relheight=1)
   canvas.create_image((0, 0), image=image1, anchor="nw")
   
   def Dosya():
       global yol
       dosya_yolu = filedialog.askopenfilename()
       yol = dosya_yolu.split("/")
   
       dosyaLabel.config(text=yol[-1])
   
       img = Image.open(dosya_yolu)
       img = img.resize((150, 150))
       img = ImageTk.PhotoImage(img)
       
   
       buton2 = Button(pencere, image=img, highlightthickness=0, borderwidth=0, bd=0, command=lambda: None)
       buton2.image = img
       buton2.place(relx=0.25, rely=0.2, relwidth=0.2, relheight=0.2)
   
   def metinOku():
       global yol
       if not yol:
           MessageBox.Show("Lütfen önce bir dosya seçin.", "Uyarı");
           return
   
       resim_yolu = yol[-1]
   
       image = cv2.imread(resim_yolu)
   
       if image is None:
           MessageBox.Show("Dosya açılamadı. Lütfen geçerli bir resim dosyası seçin.", "Uyarı");
           return
   
       image = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)
   
       kernel = np.ones((1, 1), np.uint8)
       image = cv2.erode(image, kernel, iterations=1)
       image = cv2.dilate(image, kernel, iterations=1)
   
       image = cv2.adaptiveThreshold(image, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C, cv2.THRESH_BINARY, 31, 2)
       cv2.imwrite(kaynak + 'temizlenmisResim.png', image)
   
       global sonuc
   
       sonuc = pytesseract.image_to_string(Image.open(kaynak + 'temizlenmisResim.png'), lang='tur')
   
   
       text_widget.config(state=NORMAL)
       text_widget.insert("1.0", sonuc)
       text_widget.config(state=DISABLED)
   
   dosyaLabel = Label(pencere, text="", font="Arial 21", bg="#a6a6a6")
   dosyaLabel.place(relx=0.28, rely=0.4)
   
   buton1 = Button(pencere)
   buton1.config(text="Dosya Seç", bg="#a6a6a6", fg="black", highlightthickness=0, borderwidth=0, bd=0, command=Dosya, font="Arial 21")
   buton1.place(relx=0.1, rely=0.7)
   
   buton2 = Button(pencere)
   buton2.config(text="Oku", fg="black", bg="#a6a6a6", highlightthickness=0, borderwidth=0, bd=0, font="Arial 21", command=metinOku)
   buton2.place(relx=0.35, rely=0.7)
   
   
   
   text_widget = Text(pencere, wrap=WORD, width=50, height=10, bg="#a6a6a6")
   text_widget.place(relx=0.56, rely=0.15, relwidth=0.38, relheight=0.75)
   text_widget.config(state=DISABLED) 
   
   def Kullanım():
       pencere2 = Tk()
       pencere2.title("Resimden Yazıya | Beta")
       pencere2.geometry("960x540")
       pencere2.resizable(False, False)
       pencere2.configure(bg="gray")
   
       dosyaLabel = Label(pencere2, text="""Resimdeki yazıyı okuyabilmek için öcelikle dosya
   seç butonuna tıklayarak bir dosya seçmeniz gerekiyor
   ama burada dikkat etmeniz gereken şey
   seçeceğiniz dosyanın uygulama ile aynı dizinde
   olması resim açıldığı zaman oku butonuna basarak
   yazıyı okuyabilir ve kopyalayabilirsiniz""", font="Arial 30", bg="gray")
       dosyaLabel.place(relx=0.01, rely=0.17)
   
   def kopyala():
   
       pencere.clipboard_clear()
       pencere.clipboard_append(sonuc)
   
   text_menu = Menu(pencere, tearoff=0)
   text_menu.add_command(label="Kopyala", command=kopyala)
   text_menu.add_command(label="Nasıl Kullanılır", command=Kullanım)
   text_widget.bind("<Button-3>", lambda event: text_menu.post(event.x_root, event.y_root))
   pencere.config(menu=text_menu)
   
   pencere.mainloop()

   ```

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


## Yararlandığım Kaynaklar

https://furkanuzun.wordpress.com/2018/12/22/python-opencv-ile-resimden-metin-okuma/

https://medium.com/@ibrahimirdem/tesseract-ile-yaz%C4%B1-karakteri-tan%C4%B1ma-python-8ca5e746951


