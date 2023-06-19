# kuis
Tugas Besar Alpro

package main

import (
	"bufio"
	"fmt"
	"math/rand"
	"os"
)

const NMAX int = 50

type soal struct {
	pertanyaan   string
	optionA      string
	optionB      string
	optionC      string
	optionD      string
	kunciJawaban string
	jumlahBenar  int
	jumlahSalah  int
}

type nilai struct {
	nama  string
	nilai float64
}

type Leaderboard [100]nilai

type Pertanyaan [NMAX]soal

type Peserta [NMAX]string

func inputString() string { //Inputkan teks panjang
	scan := bufio.NewScanner(os.Stdin)
	scan.Scan()
	return scan.Text()
}

func lihatSkor(arrPeringkat Leaderboard, nPeringkat int) {
	var arrTemp Leaderboard

	arrTemp = arrPeringkat

	for i := 0; i < nPeringkat; i++ {
		var minIdx = i
		for j := i; j < nPeringkat; j++ {
			if arrTemp[j].nilai > arrTemp[minIdx].nilai {
				minIdx = j
			}
		}
		arrTemp[i], arrTemp[minIdx] = arrTemp[minIdx], arrTemp[i]
	}

	for i := 0; i < nPeringkat; i++ {
		fmt.Printf("%v. %s - %v \n", i+1, arrTemp[i].nama, arrTemp[i].nilai)
	}
}

// Sub Program untuk admin

func lihatSoal(arrPertanyaan Pertanyaan, nPertanyaan int) {
	var arrTemp Pertanyaan

	arrTemp = arrPertanyaan

	for i := 0; i < nPertanyaan; i++ {
		var minIdx = i
		for j := i; j < nPertanyaan; j++ {
			if arrTemp[j].jumlahBenar > arrTemp[minIdx].jumlahBenar {
				minIdx = j
			}
		}
		arrTemp[i], arrTemp[minIdx] = arrTemp[minIdx], arrTemp[i]
	}

	fmt.Println("5 Soal dengan jawaban benar terbanyak : ")
	for i := 0; i < 5; i++ {
		fmt.Println(arrTemp[i].pertanyaan, "-", arrTemp[i].jumlahBenar, "Jawaban benar")
	}
	fmt.Println()

	fmt.Println("5 Soal dengan jawaban salah terbanyak : ")
	for i := nPertanyaan - 1; i > nPertanyaan-6; i-- {
		fmt.Println(arrTemp[i].pertanyaan, "-", arrTemp[i].jumlahSalah, "Jawaban salah")
	}
}

func tambahSoal(arrPertanyaan *Pertanyaan, nPertanyaan *int) {
	fmt.Println("Buat pertanyaan untuk soal ke -", *nPertanyaan+1)
	fmt.Printf("Pertanyaan : ")
	arrPertanyaan[*nPertanyaan].pertanyaan = inputString()
	fmt.Printf("Option A : ")
	arrPertanyaan[*nPertanyaan].optionA = inputString()
	fmt.Printf("Option B : ")
	arrPertanyaan[*nPertanyaan].optionB = inputString()
	fmt.Printf("Option C : ")
	arrPertanyaan[*nPertanyaan].optionC = inputString()
	fmt.Printf("Option D : ")
	arrPertanyaan[*nPertanyaan].optionD = inputString()

	fmt.Println("Masukkan kunci jawaban (option)!")
	fmt.Printf("Jawaban : ")
	arrPertanyaan[*nPertanyaan].kunciJawaban = inputString()

	*nPertanyaan++

	fmt.Println("Selesai menambahkan pertanyaan")
}

func editSoal(arrPertanyaan *Pertanyaan, nPertanyaan int) {
	var input, i, idx int

	for i = 0; i < nPertanyaan; i++ {
		fmt.Printf("%v. %s\n", i+1, arrPertanyaan[i].pertanyaan)
	}
	fmt.Printf("Pilih : ")
	fmt.Scanln(&idx)

	fmt.Println("- Pilihan edit -")
	fmt.Println("1. Edit soal")
	fmt.Println("2. Edit jawaban")
	fmt.Println("3. Edit keduanya")
	fmt.Printf("Pilih : ")
	fmt.Scanln(&input)

	if input == 1 {
		fmt.Printf("Pertanyaan : ")
		arrPertanyaan[idx-1].pertanyaan = inputString()
	} else if input == 2 {
		fmt.Printf("Option A : ")
		arrPertanyaan[idx-1].optionA = inputString()
		fmt.Printf("Option B : ")
		arrPertanyaan[idx-1].optionB = inputString()
		fmt.Printf("Option C : ")
		arrPertanyaan[idx-1].optionC = inputString()
		fmt.Printf("Option D : ")
		arrPertanyaan[idx-1].optionD = inputString()

		fmt.Println("Masukkan kunci jawaban (option)!")
		fmt.Printf("Jawaban : ")
		arrPertanyaan[idx-1].kunciJawaban = inputString()
	} else if input == 3 {
		fmt.Printf("Pertanyaan : ")
		arrPertanyaan[idx-1].pertanyaan = inputString()
		fmt.Printf("Option A : ")
		arrPertanyaan[idx-1].optionA = inputString()
		fmt.Printf("Option B : ")
		arrPertanyaan[idx-1].optionB = inputString()
		fmt.Printf("Option C : ")
		arrPertanyaan[idx-1].optionC = inputString()
		fmt.Printf("Option D : ")
		arrPertanyaan[idx-1].optionD = inputString()

		fmt.Println("Masukkan kunci jawaban (option)!")
		fmt.Printf("Jawaban : ")
		arrPertanyaan[idx-1].kunciJawaban = inputString()
	}
}

func hapusSoal(arrPertanyaan *Pertanyaan, nPertanyaan *int) {
	var i, idx int

	for i = 0; i < *nPertanyaan; i++ {
		fmt.Printf("%v. %s\n", i+1, arrPertanyaan[i].pertanyaan)
	}
	fmt.Printf("Pilih : ")
	fmt.Scanln(&idx)

	for i = idx - 1; i < *nPertanyaan; i++ {
		arrPertanyaan[i] = arrPertanyaan[i+1]
	}
	*nPertanyaan--
}

// Sub Program untuk peserta

func jawabPertanyaan(arrPertanyaan *Pertanyaan, nPertanyaan int, arrPeringkat *Leaderboard, nPeringkat *int, nama string) {
	var arrIdx [NMAX]int
	var i, nTemp int
	var arrTemp Pertanyaan
	var idxSoal, idxAsli int
	var jawaban string
	var nilai int

	arrTemp = *arrPertanyaan
	nTemp = nPertanyaan

	for i = 0; i < nPertanyaan; i++ {
		arrIdx[i] = i
	}

	fmt.Println("Soal Dimulai!")

	for nTemp >= 1 {
		idxSoal = rand.Intn(nTemp)
		idxAsli = arrIdx[idxSoal]

		// fmt.Println("-----------------------------------------")
		// for i = 0; i < nTemp; i++ {
		// 	fmt.Println(i+1, arrTemp[i].pertanyaan)
		// }
		// fmt.Println("-----------------------------------------")

		fmt.Printf("Pertanyaan : ")
		fmt.Println(arrTemp[idxSoal].pertanyaan)

		fmt.Printf("A. ")
		fmt.Println(arrTemp[idxSoal].optionA)
		fmt.Printf("B. ")
		fmt.Println(arrTemp[idxSoal].optionB)
		fmt.Printf("C. ")
		fmt.Println(arrTemp[idxSoal].optionC)
		fmt.Printf("D. ")
		fmt.Println(arrTemp[idxSoal].optionD)

		fmt.Printf("Jawab dengan option : ")
		fmt.Scanln(&jawaban)

		if arrTemp[idxSoal].kunciJawaban == jawaban {
			fmt.Println("ANDA BENAR!")
			arrPertanyaan[idxAsli].jumlahBenar++
			nilai++
		} else {
			fmt.Println("ANDA SALAH!")
			fmt.Printf("Jawaban yang benar adalah %s \n", arrTemp[idxSoal].kunciJawaban)
			arrPertanyaan[idxAsli].jumlahSalah++
		}

		for i = idxSoal; i < nTemp; i++ {
			arrTemp[i] = arrTemp[i+1]
			arrIdx[i] = arrIdx[i+1]
		}

		nTemp--
	}

	arrPeringkat[*nPeringkat].nama = nama
	arrPeringkat[*nPeringkat].nilai = float64(nilai) * 100 / float64(nPertanyaan)
	*nPeringkat++
}

func login(arrPeserta *Peserta, nPeserta *int) int {
	var input int
	var nama string

	fmt.Println("1. Login")
	fmt.Println("2. Buat akun")
	fmt.Printf("Pilih : ")
	fmt.Scanln(&input)

	if input == 1 {
		for i := 0; i < *nPeserta; i++ {
			fmt.Println(i+1, "-", arrPeserta[i])
		}
		fmt.Printf("Pilih : ")
		fmt.Scanln(&input)
		return input - 1
	} else {
		fmt.Printf("Masukkan nama : ")
		fmt.Scanln(&nama)
		arrPeserta[*nPeserta] = nama
		*nPeserta++
		return *nPeserta - 1
	}

}

func main() {
	var arrPertanyaan Pertanyaan
	var nPertanyaan int
	var arrPeringkat Leaderboard
	var nPeringkat int
	var arrPeserta Peserta
	var nPeserta int

	var input, input2 int
	var user int

	for true {
		fmt.Println("!WHO ONE TO BE A MILLIONAIRE!")
		fmt.Println("1. Admin")
		fmt.Println("2. Peserta")
		fmt.Printf("Pilih : ")
		fmt.Scanln(&input)

		for input != -1 {
			fmt.Println("_________________________________________________")
			if input == 1 {
				fmt.Println("1. Tambah soal")
				fmt.Println("2. Edit soal")
				fmt.Println("3. Hapus soal")
				fmt.Println("4. Leaderboard")
				fmt.Println("5. 5 soal dijawab paling benar dan salah")
				fmt.Println("6. Keluar")
				fmt.Printf("Pilih : ")
				fmt.Scanln(&input2)

				if input2 == 1 {
					tambahSoal(&arrPertanyaan, &nPertanyaan)
				} else if input2 == 2 {
					editSoal(&arrPertanyaan, nPertanyaan)
				} else if input2 == 3 {
					hapusSoal(&arrPertanyaan, &nPertanyaan)
				} else if input2 == 4 {
					lihatSkor(arrPeringkat, nPeringkat)
				} else if input2 == 5 {
					lihatSoal(arrPertanyaan, nPertanyaan)
				} else if input2 == 6 {
					input = -1
				}
			} else if input == 2 {
				user = login(&arrPeserta, &nPeserta)

				for user != -1 {
					fmt.Println("_________________________________________________")
					fmt.Println("1. Jawab quiz")
					fmt.Println("2. Leaderboard")
					fmt.Println("3. Keluar")
					fmt.Printf("Pilih : ")
					fmt.Scanln(&input2)

					if input2 == 1 {
						jawabPertanyaan(&arrPertanyaan, nPertanyaan, &arrPeringkat, &nPeringkat, arrPeserta[user])
					} else if input2 == 2 {
						lihatSkor(arrPeringkat, nPeringkat)
					} else if input2 == 3 {
						user = -1
					}
				}
				input = -1
			}
		}
	}

}
