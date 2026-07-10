# ==========================================
# ESTRUCTURAS DE DATOS INICIALES
# ==========================================

# Diccionario planes: descriptivo [cite: 18]
planes = {
    'F001': ['Plan Básico', 'mensual', 1, False, False, 'libre'],
    'F002': ['Plan Full', 'mensual', 1, True, True, 'libre'],
    'F003': ['Plan Estudiante', 'trimestral', 3, False, True, 'tarde'],
    'F004': ['Plan Senior', 'trimestral', 3, True, False, 'mañana'],
    'F005': ['Plan Anual Pro', 'anual', 12, True, True, 'libre'],
    'F006': ['Plan Nocturno', 'mensual', 1, False, True, 'noche']
}

# Diccionario inscripciones: operativo [cite: 31]
inscripciones = {
    'F001': [14990, 30],
    'F002': [22990, 10],
    'F003': [39990, 0],
    'F004': [35990, 6],
    'F005': [159990, 2],
    'F006': [18990, 15]
}


# ==========================================
# FUNCIONES AUXILIARES Y DE VALIDACIÓN (OPCIÓN 4)
# ==========================================

def validar_codigo(codigo, planes_dict):
    """Valida código: no vacío y que no exista previamente (case-insensitive)."""
    if not codigo or codigo.strip() == "":
        return False
    # Verificación insensible a mayúsculas/minúsculas
    codigos_existentes = [k.upper() for k in planes_dict.keys()]
    return codigo.upper() not in codigos_existentes

def validar_nombre(nombre):
    """Valida nombre: no vacío ni solo espacios en blanco."""
    return bool(nombre and nombre.strip())

def validar_tipo(tipo):
    """Valida tipo: debe ser exactamente 'mensual', 'trimestral' o 'anual'."""
    return tipo in ['mensual', 'trimestral', 'anual']

def validar_duracion(duracion_str):
    """Valida duración: entero mayor que cero."""
    try:
        duracion = int(duracion_str)
        return duracion > 0
    except ValueError:
        return False

def validar_piscina(opcion):
    """Valida acceso_piscina: s o n."""
    return opcion.lower() in ['s', 'n']

def validar_clases(opcion):
    """Valida incluye_clases: s o n."""
    return opcion.lower() in ['s', 'n']

def validar_horario(horario):
    """Valida horario: no vacío ni solo espacios en blanco."""
    return bool(horario and horario.strip())

def validar_precio(precio_str):
    """Valida precio: entero mayor que cero."""
    try:
        precio = int(precio_str)
        return precio > 0
    except ValueError:
        return False

def validar_cupos(cupos_str):
    """Valida cupos: entero mayor o igual a cero."""
    try:
        cupos = int(cupos_str)
        return cupos >= 0
    except ValueError:
        return False


# ==========================================
# FUNCIONES PRINCIPALES DEL SISTEMA
# ==========================================

def leer_opcion():
    """Solicita, valida y retorna una opción entera del menú."""
    try:
        opcion = int(input("Ingrese opción: "))
        if 1 <= opcion <= 6:
            return opcion
        else:
            print("Debe seleccionar una opción válida")
            return None
    except ValueError:
        print("Debe seleccionar una opción válida")
        return None

def cupos_tipo(tipo, planes_dict, inscripciones_dict):
    """Opción 1: Muestra el total de cupos para un tipo de plan específico."""
    total_cupos = 0
    tipo_buscado = tipo.lower()
    
    # Recorremos planes_dict buscando coincidencia de tipo
    for codigo, datos in planes_dict.items():
        if datos[1].lower() == tipo_buscado:
            # Buscamos los cupos en inscripciones_dict usando la misma clave
            if codigo in inscripciones_dict:
                total_cupos += inscripciones_dict[codigo][1]
                
    print(f"El total de cupos disponibles es: {total_cupos}")

def busqueda_precio(p_min, p_max, planes_dict, inscripciones_dict):
    """Opción 2: Busca planes dentro de un rango de precio con cupos disponibles."""
    resultados = []
    
    for codigo, datos_ins in inscripciones_dict.items():
        precio = datos_ins[0]
        cupos = datos_ins[1]
        
        if p_min <= precio <= p_max and cupos > 0:
            # Si cumple, extraemos el nombre del plan desde el diccionario planes
            if codigo in planes_dict:
                nombre_plan = planes_dict[codigo][0]
                resultados.append(f"{nombre_plan}--{codigo}")
                
    if resultados:
        # Los resultados se ordenan alfabéticamente por el nombre del plan
        resultados.sort()
        print(f"Los planes encontrados son: {resultados}")
    else:
        print("No hay planes en ese rango de precios.")

def buscar_codigo(codigo, inscripciones_dict):
    """Función de apoyo: Verifica si un código existe (case-insensitive)."""
    codigos_en_mayuscula = [k.upper() for k in inscripciones_dict.keys()]
    return codigo.upper() in codigos_en_mayuscula

def actualizar_precio(codigo, nuevo_precio, inscripciones_dict):
    """Opción 3: Actualiza el precio de un plan si el código existe."""
    # Buscamos el código real respetando las mayúsculas/minúsculas originales
    for clave_real in inscripciones_dict.keys():
        if clave_real.upper() == codigo.upper():
            inscripciones_dict[clave_real][0] = nuevo_precio
            return True
    return False

def agregar_plan(codigo, nombre, tipo, duracion, acceso_piscina, incluye_clases, horario, precio, cupos, planes_dict, inscripciones_dict):
    """Opción 4: Inserta de manera efectiva el nuevo plan en ambos diccionarios."""
    # Convertir las opciones s/n a booleanos tal como pide el enunciado
    piscina_bool = True if acceso_piscina.lower() == 's' else False
    clases_bool = True if incluye_clases.lower() == 's' else False
    
    # Se añade a planes
    planes_dict[codigo] = [nombre, tipo, int(duracion), piscina_bool, clases_bool, horario]
    # Se añade a inscripciones
    inscripciones_dict[codigo] = [int(precio), int(cupos)]
    return True

def eliminar_plan(codigo, planes_dict, inscripciones_dict):
    """Opción 5: Elimina el registro del plan en ambos diccionarios."""
    clave_a_eliminar = None
    for clave_real in planes_dict.keys():
        if clave_real.upper() == codigo.upper():
            clave_a_eliminar = clave_real
            break
            
    if clave_a_eliminar:
        del planes_dict[clave_a_eliminar]
        if clave_a_eliminar in inscripciones_dict:
            del inscripciones_dict[clave_a_eliminar]
        return True
    return False


# ==========================================
# PROGRAMA PRINCIPAL
# ==========================================

def main():
    while True:
        print("\n========== MENÚ PRINCIPAL ==========")
        print("1. Cupos por tipo de plan")
        print("2. Búsqueda de planes por rango de precio")
        print("3. Actualizar precio de plan")
        print("4. Agregar plan")
        print("5. Eliminar plan")
        print("6. Salir")
        print("=====================================")
        
        opcion = leer_opcion()
        if opcion is None:
            continue
            
        if opcion == 1:
            tipo = input("Ingrese tipo de plan a consultar: ")
            cupos_tipo(tipo, planes, inscripciones)
            
        elif opcion == 2:
            while True:
                try:
                    p_min = int(input("Ingrese precio mínimo: "))
                    p_max = int(input("Ingrese precio máximo: "))
                    if p_min >= 0 and p_max >= 0 and p_min <= p_max:
                        break
                    else:
                        print("Debe ingresar valores válidos (mayores a cero y min <= max)")
                except ValueError:
                    print("Debe ingresar valores enteros")
            
            busqueda_precio(p_min, p_max, planes, inscripciones)
            
        elif opcion == 3:
            while True:
                cod = input("Ingrese código del plan: ")
                try:
                    nuevo_p = int(input("Ingrese nuevo precio: "))
                    if nuevo_p <= 0:
                        print("El precio debe ser un entero positivo.")
                        continue
                except ValueError:
                    print("Debe ingresar valores enteros")
                    continue
                
                # Se llama a la lógica de actualización
                if actualizar_precio(cod, nuevo_p, inscripciones):
                    print("Precio actualizado")
                else:
                    print("El código no existe")
                    
                resp = input("¿Desea actualizar otro precio (s/n)?: ")
                if resp.lower() != 's':
                    break
                    
        elif opcion == 4:
            # Captura de datos
            c_cod = input("Ingrese código del plan: ")
            c_nom = input("Ingrese nombre del plan: ")
            c_tip = input("Ingrese tipo (mensual/trimestral/anual): ")
            c_dur = input("Ingrese duración (meses): ")
            c_pis = input("¿Incluye acceso a piscina? (s/n): ")
            c_cla = input("¿Incluye clases grupales? (s/n): ")
            c_hor = input("Ingrese horario: ")
            c_pre = input("Ingrese precio: ")
            c_cup = input("Ingrese cupos: ")
            
            # Ejecución secuencial de validaciones independientes
            if not validar_codigo(c_cod, planes):
                print("El código ya existe o es inválido")  # Mensaje en el main()
            elif not validar_nombre(c_nom):
                print("Nombre inválido")
            elif not validar_tipo(c_tip):
                print("Tipo inválido")
            elif not validar_duracion(c_dur):
                print("Duración inválida")
            elif not validar_piscina(c_pis):
                print("Opción de piscina inválida")
            elif not validar_clases(c_cla):
                print("Opción de clases inválida")
            elif not validar_horario(c_hor):
                print("Horario inválido")
            elif not validar_precio(c_pre):
                print("Precio inválido")
            elif not validar_cupos(c_cup):
                print("Cupos inválidos")
            else:
                # Si pasa todo, se agrega con éxito
                agregar_plan(c_cod, c_nom, c_tip, c_dur, c_pis, c_cla, c_hor, c_pre, c_cup, planes, inscripciones)
                print("Plan agregado")
                
        elif opcion == 5:
            cod_eliminar = input("Ingrese el código del plan que desea eliminar: ")
            if eliminar_plan(cod_eliminar, planes, inscripciones):
                print("Plan eliminado")
            else:
                print("El código no existe")
                
        elif opcion == 6:
            print("Programa finalizado.")
            break

# Ejecución del programa principal
if __name__ == "__main__":
    main()
