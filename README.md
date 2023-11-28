# PracticaFinal
import tkinter as tk
import re
from PIL import Image, ImageTk
import io
import zipfile
ruta_emoticones = '/ruta/completa/hacia/emojicshd.zip'
ruta_diccionario = 'ruta/del/diccionario/diccionario_espanol.txt'

with zipfile.ZipFile(ruta_emoticones, 'r') as zip_ref:
    lista_emoticones = {name: zip_ref.read(name) for name in zip_ref.namelist()}

def cargar_diccionario():
    with open(ruta_diccionario, 'r', encoding='utf-8') as file:
        return set(word.strip().lower() for word in file.readlines())

def analizador_lexicografico(cadena, diccionario_espanol):
    patron_palabras_espanol = re.compile(r'\b[a-zA-ZáéíóúüÁÉÍÓÚÜñÑ]+\b')

    patron_emojis = re.compile(r'(:\)|:\(|:D|;\)|:P|xD|:-\)|:-\(|\(y\)|\(n\)|<3|\\m/|:-O|:O|:-\||:\||:\*|>:\(|\^\^|:-\])')
    patron_variantes_emojis = re.compile(r'(:-?\)|:-?\(|\(y\)|\(n\)|<3|\\m/|:-?O|:-?\||:\*|>:\(|\^\^|:-?\])')

    emojis_encontrados = patron_emojis.findall(cadena)
    variantes_emojis_encontrados = patron_variantes_emojis.findall(cadena)
    emojis = list(set(emojis_encontrados + variantes_emojis_encontrados))

    palabras_encontradas = patron_palabras_espanol.findall(cadena)
    palabras_espanol = [palabra.lower() for palabra in palabras_encontradas if palabra.lower() in diccionario_espanol]

    return palabras_espanol, emojis

def procesar_cadena():
    texto_entrada = entrada_texto.get("1.0", tk.END)

    palabras, emojis = analizador_lexicografico(texto_entrada, diccionario_espanol)

    resultado_texto.delete("1.0", tk.END)
    resultado_texto.insert(tk.END, f"Cadena de entrada:\n{texto_entrada}\n\n")
    resultado_texto.insert(tk.END, f"Palabras en español encontradas: {len(palabras)}\n")
    resultado_texto.insert(tk.END, f"Emojis encontrados: {len(emojis)}\n")

    # Mostrar los emoticones en la interfaz gráfica
    for emoji in emojis:
        if emoji in lista_emoticones:
            image_data = lista_emoticones[emoji]
            image = Image.open(io.BytesIO(image_data))
            photo = ImageTk.PhotoImage(image)
            label = tk.Label(frame_emojis, image=photo)
            label.image = photo  
            label.pack()

    # Mostrar la cadena de salida
    resultado_texto_emoticones.delete("1.0", tk.END)
    resultado_texto_emoticones.insert(tk.END, texto_entrada)

root = tk.Tk()
root.title("Analizador Lexicográfico")

frame_entrada = tk.Frame(root)
frame_entrada.pack(padx=10, pady=10)

etiqueta = tk.Label(frame_entrada, text="Ingrese la cadena a procesar:")
etiqueta.pack()

entrada_texto = tk.Text(frame_entrada, height=5, width=50)
entrada_texto.pack()

boton_procesar = tk.Button(root, text="Procesar", command=procesar_cadena)
boton_procesar.pack(pady=10)

resultado_texto = tk.Text(root, height=10, width=50)
resultado_texto.pack()

frame_emojis = tk.Frame(root)
frame_emojis.pack()

diccionario_espanol = cargar_diccionario()
resultado_texto_emoticones = tk.Text(root, height=10, width=50)
resultado_texto_emoticones.pack()

root.mainloop()
