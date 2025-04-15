# instruksi-kondisi
```nasm
section .text
    global _start

_start:
    mov     eax, [angka1]        ; simpan angka pertama di eax
    mov     ebx, [angka2]        ; simpan angka kedua di ebx
    cmp     eax, ebx
    jg      check_angka_ketiga
    mov     eax, ebx             ; angka kedua lebih besar

check_angka_ketiga:
    mov     ebx, [angka3]
    cmp     eax, ebx
    jg      _exit
    mov     eax, ebx             ; angka ketiga lebih besar

_exit:
    mov     [terbesar], eax      ; simpan angka terbesar

    ; cetak pesan
    mov     ecx, msg
    mov     edx, len
    mov     ebx, 1               ; stdout
    mov     eax, 4               ; sys_write
    int     0x80

    ; konversi angka (eax = terbesar) ke string
    mov     eax, [terbesar]
    mov     edi, buffer + 9
    mov     byte [edi], 0        ; null terminator

convert_loop:
    mov     edx, 0
    mov     ecx, 10
    div     ecx
    add     dl, '0'
    dec     edi
    mov     [edi], dl
    test    eax, eax
    jnz     convert_loop

    ; tampilkan angka
    mov     eax, 4
    mov     ebx, 1
    mov     ecx, edi
    mov     edx, buffer + 10
    sub     edx, edi
    int     0x80

    ; keluar
    mov     eax, 1
    xor     ebx, ebx
    int     0x80

section .data
    msg db 'angka yang paling besar adalah ', 0xA, 0xD
    len equ $- msg
    angka1 dd 52
    angka2 dd 33
    angka3 dd 12

section .bss
    terbesar resd 1
    buffer resb 10
    
```
![Screenshot 2025-04-15 091852](https://github.com/user-attachments/assets/f2a169a3-55cc-45c8-8d64-fc35b8cd89c5)

- Program ini membandingkan tiga angka: 52, 33, dan 12. Ia mencari angka terbesar di antara ketiganya, yaitu 52. Dengan hasil output nya yaitu mencetak pesan: angka yang paling besar adalah 52.


## Analisis dari codingan Instruksi Kondisi diatas
- section .text: Bagian ini berisi kode program (instruksi yang dijalankan).
- global _start: Mendeklarasikan label _start sebagai entry point (fungsi utama) untuk program ini. Ini diperlukan untuk sistem operasi tahu dari mana memulai eksekusi.
- _start: Label sebagai titik awal eksekusi.
- mov eax, [angka1] : Memindahkan nilai dari angka1 ke register ECX.
- mov ebx, [angka2] : Nilai di angka2 dimasukkan ke EBX, menimpa isi sebelumnya.
- cmp eax,ebx : nstruksi ini hanya membandingkan, tidak mengubah nilai register.
- jg check_angka_ketiga : Jump if Greater – lompat ke check_angka_ketiga jika nilai sebelumnya lebih besar.
- mov eax, ebx : Jika EAX <= EBX, maka kita simpan angka2 sebagai nilai terbesar sementara di EAX.
- pada bagian chek_angka_ketiga : Label, tempat pemeriksaan angka ketiga dimulai.
- mov ebx, [angka3] : Ambil nilai angka3 dan simpan ke EBX.
- cmp eax, ebx : Bandingkan nilai terbesar sementara (EAX) dengan angka3 (EBX)
- jg _exit : Jika EAX > EBX, kita lanjut ke label _exit (berarti angka3 bukan yang terbesar).
- mov eax, ebx : Jika tidak, berarti angka3 lebih besar → simpan angka3 sebagai nilai terbesar di EAX.
- _exit : Label tempat eksekusi berakhir dan output dimulai.
- mov [terbesar], eax : Simpan nilai terbesar dari EAX ke variabel terbesar di memori (bagian .bss).
- mov ecx, msg : Mengisi ECX dengan alamat dari string msg.
- mov edx, len : Mengisi EDX dengan panjang string msg.
- mov ebx, 1 : Menentukan file descriptor 1 (stdout) untuk output.
- mov ebx, 4 : Menimpa EBX sebelumnya dengan angka syscall 4 (write).
- int 0x80 : Memanggil interrupt Linux untuk menjalankan syscall (write ke layar).
- mov eax, terbesar : Ambil angka terbesar dari memori, taruh di EAX.
- mov edi, buffer + 9 : EDI diarahkan ke akhir buffer untuk menyimpan karakter dari belakang.
- mov byte [edi], 0 : Tambahkan null terminator (akhir dari string, seperti di bahasa C).
- convert_loop:
  mov edx, 0 : Bersihkan EDX, karena DIV menggunakan EDX:EAX.
- mov ecx,10
  div ecx : Bagi eax dengan 10, dan hasil pembagian masuk EAX, sisa di EDX.
- add d1, '0' : Ubah angka sisa ke ASCII
- dec edi
  mov [edi], d1 : Masukkan karakter ASCII ke buffer dari belakang.
- test eax, eax
  jnz convert_loop : Mengecek apakah EAX sudah nol. Jika belum, ulangi konversi.
- mov  eax, 4
  mov  ebx, 1
  mov  ecx, edi : Siapkan untuk mencetak hasil konversi (alamat awal string di EDI).
- mov  edx, buffer + 10
  sub  edx, edi : Hitung panjang string (dari EDI ke akhir buffer).
- int 0x80 : Menampilkan nilai ke layar 
- mov eax, 1
  xor ebx, ebx
  int 0x80 : Syscall 1 = exit
             Nilai EBX = 0 sebagai exit code
             Program selesai
- section .data : Bagian untuk data tetap yang telah didefinisikan sebelumnya.
- msg db 'angka yang paling besar adalah ', 0xA, 0xD : String yang akan ditampilkan di layar.
- len equ $ - msg : Mendefinisikan panjang string msg.
- angka1 dd '52' : Angka pertama, ditulis sebagai string '52'.
- angka2 dd '33' : Angka kedua.
- angka3 dd '12' : Angka ketiga.
- section .bss : Bagian untuk variabel yang belum diinisialisasi.
- terbesar resb 1 : Reservasi 1 byte di memori untuk menyimpan nilai terbesar.
- buffer resb 10 : Buffer untuk menyimpan hasil angka dalam bentuk teks (maksimal 10 digit).

