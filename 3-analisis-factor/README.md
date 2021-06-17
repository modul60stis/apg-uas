# Analisis Faktor

### Contoh 1

Misal disini akan melakukan analisis faktor untuk data berikut [disini](./data_survery_pjj.csv)

|X1 |X2 |X3 |X4 |X5 |X6 |
|---|---|---|---|---|---|
|4  |5  |4  |3  |4  |5  |
|3  |3  |4  |3  |3  |3  |
|4  |3  |4  |4  |3  |4  |
|2  |5  |5  |4  |5  |5  |
|3  |5  |5  |4  |2  |3  |

Data berupa skala likert 1-5 dengan variabelnya sebagai berikut

1.	Saya tidak memiliki kendala dalam mengakses internet.
2.	Saya terbiasa mencari informasi dari internet.
3.	Saya sudah tahu mengenai Google Classroom sebelumnya sehingga bermanfaat saat pelaksanaan PJJ.
4.	Saya memiliki pengetahuan tentang Zoom, Google Meet ataupun layanan yang sejenis sebelumnya sehingga bermanfaat saat PJJ
5.	Saya tidak masalah seandainya PJJ ini dilanjutkan.
6.	Saya tidak masalah untuk tetap menggunakan Google Classroom untuk pelaksanaan PJJ.


Misal terdapat case bahwa data kurang, dan dibutuhkan resampling dapat melakukan hal berikut

```r
set.seed(1)
indeks_sampel <- sample(1:100, size=300, replace=TRUE)
resample <- data[indeks_sampel,]
```

Maksudnya, dari data awal tersebut diambil data secara acak sebanyak 300 record dengan menggunakan indeksnya.


Kemudian data tersebut, distandarisasi dengan menggunakan fungsi `scale`
```r
strandarized_data = data.frame(scale(resample))
```

kemudian, hal yang pertama dilakukan yaitu uji kenormalan multivariate. Uji kenormalan multivariate dilakukan dengan menggunakan library `MVN` dan fungsi  `mvn`. Hasil dari pengujian nya dapat dilihat sebagai berikut:
```r
library(MVN)
result = mvn(strandarized_data)
```

```
$multivariateNormality
             Test        Statistic              p value Result
1 Mardia Skewness 613.802606757308 7.34997351207197e-95     NO
2 Mardia Kurtosis 7.24881038020658 4.20552481728009e-13     NO
3             MVN             <NA>                 <NA>     NO


$univariateNormality
          Test  Variable Statistic   p value Normality
1 Shapiro-Wilk    X1        0.9016  <0.001      NO    
2 Shapiro-Wilk    X2        0.8287  <0.001      NO    
3 Shapiro-Wilk    X3        0.8352  <0.001      NO    
4 Shapiro-Wilk    X4        0.8486  <0.001      NO    
5 Shapiro-Wilk    X5        0.9073  <0.001      NO    
6 Shapiro-Wilk    X6        0.8800  <0.001      NO    

$Descriptives
     n          Mean Std.Dev      Median       Min      Max        25th
X1 300  1.020957e-16       1 -0.24347279 -1.962104 1.475159 -1.10278854
X2 300 -2.104617e-17       1  0.04641465 -2.485294 1.312269  0.04641465
X3 300 -2.130342e-16       1  0.06549667 -3.864303 1.375430  0.06549667
X4 300  2.280673e-16       1 -0.33215026 -2.704652 2.040352 -0.33215026
X5 300  1.575425e-16       1 -0.16817329 -2.186253 1.849906 -0.16817329
X6 300  1.469445e-16       1  0.32580518 -2.793606 1.365609 -0.71399858
         75th       Skew   Kurtosis
X1 0.61584295 -0.3468872 -0.7791151
X2 0.04641465 -0.5845785  0.1223693
X3 0.06549667 -0.5006858  0.3273621
X4 0.85410067 -0.2285180 -0.8600612
X5 0.84086644 -0.1308939 -0.4626018
X6 0.32580518 -0.4227748 -0.4796645
```

Hasil yang diperoleh adalah kenormalan multivariate, kenormalan univariate, dan deskriptif nya. Kemudian diperoleh Nilai `p−value<0.5` baik untuk yang univariate maupun yang multivariate, sehingga keputusannya adalah Tolak H0 artinya data tidak berdistribusi normal. Namun, pada kasus ini, data diasumsikan berdistribusi normal 😅.

Kemudian lakukan uji ` Sphericity Bartlett` menggunakan package `REdas` untuk menguji apakah terdapat korelasi yang signifikan antar variable penelitian. Uji Bartlett’s Sphericity digunakan untuk menguji apakah terdapat korelasi yang signifikan antar variable penelitian. Secara teknis, Uji Bartlett’s Sphericity menguji apakah matriks korelasi adalah matriks identitas atau bukan. P-value harus signifikan: yaitu, tolak hipotesis nol bahwa semua korelasi diluar diagonal utama adalah nol.

```r
library(REdaS)
bart_spher(strandarized_data)
```

```
 Bartlett's Test of Sphericity

Call: bart_spher(x = strandarized_data)

     X2 = 426.718
     df = 15
p-value < 2.22e-16
```

Dari hasil di atas dapat dilihat bahwa `p−value<0.05` maka `tolak H0` artinya tidak terdapat korelasi yang signifikan antar variabel penelitian.


Selanjutnya dilakukan uji kecukupan sampel KMO dan MSA.

> Kriteria kecukupan pengambilan sampel Kaiser-Meyer-Olkin (biasanya disingkat KMO) dengan MSA (ukuran individu dari kecukupan pengambilan sampel untuk setiap item) dilakukan untuk menguji apakah ada sejumlah faktor yang signifikan dalam kumpulan data:
> Secara teknis, KMO menguji rasio korelasi item terhadap korelasi item parsial. Jika parsial mirip dengan korelasi mentah, itu berarti item tersebut tidak berbagi cukup banyak varian dengan item lain.
> Kisaran KMO adalah dari 0,0 hingga 1,0 dan nilai yang diinginkan `>0,5`. Variabel dengan MSA `<0,5` menunjukkan bahwa item tidak termasuk dalam kelompok dan dapat dihapus dari analisis faktor.

Untuk melakukan uji kecukupan sampel menggunakan KMO dan MSA dapat menggunakan fungsi `KMOS` seperti berikut

```r
KMOS(strandarized_data)
```

```
Kaiser-Meyer-Olkin Statistics

Call: KMOS(x = strandarized_data)

Measures of Sampling Adequacy (MSA):
       X1        X2        X3        X4        X5        X6 
0.5583560 0.6249516 0.5013932 0.5885324 0.5989266 0.5852448 

KMO-Criterion: 0.5815603
```

Hasil yang diperoleh nilai `KMO>0.5` yang berarti data memenuhi kriteria pengambilan sampel dan nilai `MSA >0.5`  pada semua variabel yang berarti semua variabel termasuk dalam kelompok dan masuk dalam analisis faktor.

Selanjutnya akan dilakukan analisis faktor. yang pertama kita lakukan adalah untuk mencari berapa faktor yang harus di ekstrak dengan menggunakan `screeplot`, disini bisa kita gunakan library `psych` seperti berikut:

```r
library(psych)
scree(cor(strandarized_data))
```

![screeplot](./img/screeplot.jpeg)

Hasil dari screeplot di atas menunjukkan jumlah factor yang harus kita ekstrak adalah 3, karena yang paling mendekati garis PC nya. Kemudian dari kita analisis faktor dengan jumlah faktor 3. Analisis faktor dapat dilakukan dengan menggunakan fungsi `factanal`

```r
factanal(strandarized_data, factors = 3, scores = "regression")
```

```
Call:
factanal(x = strandarized_data, factors = 3, scores = "regression",     rotation = "varimax")

Uniquenesses:
   X1    X2    X3    X4    X5    X6 
0.005 0.563 0.005 0.839 0.539 0.005 

Loadings:
   Factor1 Factor2 Factor3
X1         0.993          
X2 0.291   0.545   0.238  
X3                 0.994  
X4 0.117   0.100   0.370  
X5 0.594   0.191   0.267  
X6 0.987   0.132          

               Factor1 Factor2 Factor3
SS loadings      1.441   1.347   1.256
Proportion Var   0.240   0.225   0.209
Cumulative Var   0.240   0.465   0.674

The degrees of freedom for the model is 0 and the fit was 0.0079
```

Diperoleh pada nilai loadings nya

1. Faktor 1 didominasi oleh variabel X5 dan X6, sehingga faktor 1 merepresentasikan PJJ tetap dilaksanakan menggunakan Google Classroom.
2. Faktor 2 didominasi oleh variabel X1 dan X2, sehingga faktor 2 merepresentasikan mahasiswa sudah terbiasa mencari informasi di internet.
3. Faktor 3 didominasi oleh variabel X3 dan X4, sehingga faktor 3 merepresentasikan mahasiswa sudah memahami aplikasi pendukung PJJ.
