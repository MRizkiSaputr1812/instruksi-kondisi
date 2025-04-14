# instruksi-kondisi
```nasm
section .text
    global _start

_start:
    ; Load the first two numbers into registers
    mov     eax, [angka1]  ; Load angka1 into eax
    mov     ebx, [angka2]  ; Load angka2 into ebx

    ; Compare angka1 and angka2
    cmp     eax, ebx
    jg      check_angka_ketiga  ; If angka1 > angka2, jump to check_angka_ketiga

    ; If angka2 is greater or equal, move angka2 to eax
    mov     eax, ebx

check_angka_ketiga:
    ; Load angka3 into ecx
    mov     ecx, [angka3]

    ; Compare the current largest (in eax) with angka3
    cmp     eax, ecx
    jg      _exit  ; If current largest > angka3, jump to _exit

    ; If angka3 is greater, move it to eax
    mov     eax, ecx

_exit:
    ; Store the largest number in 'terbesar'
    mov     [terbesar], eax

    ; Prepare to print the message
    mov     edx, len
    mov     ecx, msg
    mov     ebx, 1      ; stdout
    mov     eax, 4      ; sys_write
    int     0x80

    ; Prepare to print the largest number
    mov     eax, [terbesar]  ; Load the largest number into eax
    add     eax, '0'         ; Convert to ASCII
    mov     [terbesar], eax   ; Store back to 'terbesar'

    mov     edx, 1          ; Length of the number to print (1 byte)
    mov     ecx, terbesar    ; Pointer to the number
    mov     ebx, 1          ; stdout
    mov     eax, 4          ; sys_write
    int     0x80

    ; Exit the program
    mov     eax, 1          ; sys_exit
    xor     ebx, ebx        ; Return 0
    int     0x80

section .data
    msg db 'angka yang paling besar adalah ', 0xA, 0xD
    len equ $ - msg
    angka1 dd 52            ; Use actual numbers instead of strings
    angka2 dd 33
    angka3 dd 12

section .bss
    terbesar resb 1         ; Reserve 1 byte for the largest number
```
<img width="236" alt="image" src="https://github.com/user-attachments/assets/b284eb53-3aea-45a1-a050-cf1e555350ef" />


## Analisis dari codingan Instruksi Kondisi diatas
- section .text: Bagian ini berisi kode program (instruksi yang dijalankan).
- global _start: Mendeklarasikan label _start sebagai entry point (fungsi utama) untuk program ini. Ini diperlukan untuk sistem operasi tahu dari mana memulai eksekusi.

- _start: Label sebagai titik awal eksekusi.
- mov ecx, [angka1] : Memindahkan nilai dari angka1 ke register ECX.
- mov ecx, [angka2] : Nilai di angka2 dimasukkan ke ECX, menimpa isi sebelumnya.
- jg check_angka_ketiga : Jump if Greater – lompat ke check_angka_ketiga jika nilai sebelumnya lebih besar.
- mov ecx, [num2] : Mengambil nilai dari num2 ke ECX.
- pada bagian chek_angka_ketiga : Label (salah ketik dari check_angka_ketiga), tempat pemeriksaan angka ketiga dimulai.
- cmp ecx, [angka3] : Membandingkan isi ECX dengan angka3
- jg _exit : Jika ECX lebih besar dari angka3, lompat ke _exit.
- mov ecx, [amgka3] : Mengisi ECX dengan angka3.
- _exit : Label tempat eksekusi berakhir dan output dimulai.
- mov [terbesar], ecx : Nilai terbesar yang sudah dibandingkan disimpan ke variabel terbesar.
- mov ecx, msg : lMengisi ECX dengan alamat dari string msg.
- mov edx, len : Mengisi EDX dengan panjang string msg.
- mov ebx, 1 : Menentukan file descriptor 1 (stdout) untuk output.
- mov ebx, 4 : Menimpa EBX sebelumnya dengan angka syscall 4 (write).
- int 0x80 : Memanggil interrupt Linux untuk menjalankan syscall (write ke layar).
- mov ecx, terbesar : Mengisi ECX dengan alamat dari terbesar.
- mov edx, 2 : Menyatakan bahwa 2 byte akan ditampilkan.
- mov ebx, 1 : File descriptor untuk stdout.
- mov eax, 4 : Nomor syscall untuk write.
- int 0x80 : Menampilkan nilai terbesar ke layar (sebagai output mentah, belum diubah jadi string angka).
- mov eax, 1 : Syscall nomor 1 untuk keluar dari program.
- int 80h : Menjalankan syscall exit.
- section .data : Bagian untuk data tetap yang telah didefinisikan sebelumnya.
- msg db 'angka yang paling besar adalah ', 0xA, 0xD : String yang akan ditampilkan di layar.
- len equ $ - msg : Mendefinisikan panjang string msg.
- angka1 dd '52' : Angka pertama, ditulis sebagai string '52'.
- angka2 dd '33' : Angka kedua.
- angka3 dd '12' : Angka ketiga.
- segment .bss : Bagian untuk variabel yang belum diinisialisasi.
- terbesar resb 2 : Reservasi 2 byte di memori untuk menyimpan nilai terbesar.
