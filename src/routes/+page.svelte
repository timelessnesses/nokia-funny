<script lang="ts">
    import "../css/bootstrap.css"
    import "../css/index.css"
    import {onMount} from "svelte"
    import { Icon } from "svelte-awesome"
    import { faGithub } from "@fortawesome/free-brands-svg-icons/faGithub"
    interface Dictionary<T> {
        [Key: string]: T;
    }

    let alphabets = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("")
    let numbers = [
        1,2,3,4,5,6,7,8,9,0
    ]

    interface a {
        [key: string]: string
    }

    let alphabet_to_number: Dictionary<string> = {}    
    let count = 1
    let number_count = 1

    function is_upper_case(letter: string) {
        return letter === letter.toUpperCase()
    }

    alphabets.forEach((alphabet, index) => { // uppercases
        if (count > 3) {
            count = 1
            number_count++
        }
        if (number_count > 10) {
            number_count = 1
        }
        let x = numbers[number_count - 1].toString()
        let amount = ""
        amount += "#" // specialty for uppercase
        for(let i = 0; i < count + 1; i++) {
            amount += x.toString()
        }
        alphabet_to_number[alphabet] = amount
        count++
    })

    count = 1
    number_count = 1

    alphabets.forEach((alphabet, index) => { // lowercases
        alphabet = alphabet.toLowerCase()
        if (count > 3) {
            count = 1
            number_count++
        }
        if (number_count > 10) {
            number_count = 1
        }
        let x = numbers[number_count - 1].toString()
        let amount = ""
        for(let i = 0; i < count + 1; i++) {
            amount += x.toString()
        }
        alphabet_to_number[alphabet] = amount
        count++
    })

    numbers.forEach((number, index) => {
        alphabet_to_number[number.toString()] = number.toString()
    })

    alphabet_to_number[" "] = "00"
    alphabet_to_number["+"] = "**"

    function reverse_dict(dict: Dictionary<string>) {
        let new_dict: Dictionary<string> = {}
        Object.keys(dict).forEach((key) => {
            new_dict[dict[key]] = key
        })
        return new_dict
    }
    let number_to_alphabet = reverse_dict(alphabet_to_number)

    function is_number(text: string) {
        return !isNaN(Number(text)) && text !== " "
    }

    function encoder(text: string): string {
        // if it's number then just leave it
        let encoded = ""
        for(let i = 0; i < text.length; i++) {
            let letter = text[i]
            console.log(alphabet_to_number[letter])
            console.log(`"${letter}"`)
            if (is_number(letter)) {
                encoded += " "+letter
                continue
            }
            encoded += " " + alphabet_to_number[letter]
        }
        return encoded
    }

    function decoder(text: string): string {
        let decoded = ""

        let splitted = text.split(" ")

        splitted.forEach((letter, index) => {
            if (letter === "") {
                return
            }
            if (is_number(letter)) {
                decoded += number_to_alphabet[letter]
                return
            }
            if (letter[0] === "#") {
                letter = letter.slice(1)
                decoded += " " + number_to_alphabet[letter].toUpperCase()
                return
            }
            decoded += " " + number_to_alphabet[letter]
        })

        return decoded
    }
    let encode_choice: HTMLInputElement
    let decode_choice: HTMLInputElement
    let error_html: HTMLHeadingElement
    function checker(event: Event) {
        event.preventDefault()
        let text = (document.getElementById("text") as HTMLInputElement).value
        let encoded = ""
        console.log(text)
        if (encode_choice.checked) {
            encoded = encoder(text)
        } else if (decode_choice.checked) {
            encoded = decoder(text)
        }
        else {
            error_html.style.visibility = "visible"
            sleep(2000).then(() => {
                error_html.style.visibility = "hidden"
            })
        }
        (document.getElementById("result") as HTMLInputElement).value = encoded
    }
    function sleep(ms: number) {
        return new Promise(resolve => setTimeout(resolve, ms));
    }
    onMount(() => {
        // @ts-ignore
        encode_choice = document.getElementById("encode") as HTMLInputElement
        decode_choice = document.getElementById("decode") as HTMLInputElement
        encode_choice.checked = true
        window.encoder = encoder
        window.decoder = decoder
    })

</script>
<img src="/image0.gif" alt="Nokia funny" class="center">
<h1>Nokia num pad text encoder/decoder (why?)</h1>

<form on:submit={checker}>

    <textarea id="text" name="text" rows="4" cols="50" placeholder="Text to encode/decode"></textarea><br>
    <input type="radio" bind:this={encode_choice} value="encode" id="encode" name="choice" checked>
    <label for="encode">Encode</label><br>
    <input type="radio" bind:this={decode_choice} value="decode" id="decode" name="choice">
    <label for="decode">Decode</label><br>
    <button type="submit" class="btn btn-success">Run!</button>
    <h3 id="error" bind:this={error_html}>Something went wrong. Please try again</h3>
</form>

<textarea id="result" name="result" rows="4" cols="50" placeholder="Result" readonly></textarea><br>

<a href="https://github.com/timelessnesses/nokia-funny"><Icon data="{faGithub}"/></a>