# TwinCAT Dynamic String Kit

A library that provides tools for working with dynamic strings in TwinCAT. It includes a comprehensive string builder with string enumeration and a read-only string builder adapter. Additionally, there are several C-style string functions, such as `isdigit(...)`, `strstr(...)`, `strlcpy(...)`, `isspace(...)`, and more. There are also functions for converting `ANY` to `STRING` and `STRING` to `ANY`.

# Examples

Working with the string builder.

**Declarations:** 
```ST
VAR
	bStart              : BOOL;
	nLn1, nLn2          : T_Size;
	sValue1, sValue2    : STRING(255);
	pString             : POINTER TO STRING;
	fbString_Builder1,
	fbString_Builder2   : FB_String_Builder;
END_VAR
```

**Implementation:**
```ST
IF bStart THEN
	bStart := FALSE;
	fbString_Builder2
		.Append(' I hate cats.')
		.Get_Buffer(pString);
	
	fbString_Builder1
		.Clear()
		.Append(' I love cats.')
		.Append(' I love dogs.')
		.Append_Buffer(pString)
		.Trim_Whitespace(T_Whitespace_Location.Both)
		.Get_Length(Length => nLn1)
		.Replace('cats','dogs',TRUE)
		.Insert(nLn1, ' Anaconda! ')
		.Split(nLn1,100, fbString_Builder2.Append(' Tigers are cats.'))
		.Get_Length(Length => nLn1);
	
	sValue1 := fbString_Builder1.To_Upper_Case().Get_String();
	sValue2 := fbString_Builder2
				.To_Lower_Case()
			   	.Trim_Whitespace(T_Whitespace_Location.Both)
				.Get_String();
	
	nLn2 := Tc2_Standard.LEN(sValue1);
	__DELETE(pString);
	END_IF
```

**Output:**

![string builder](./assets/images/string%20builder.png)

---

Working with the read-only adapter.

**Declarations:** 
```ST
VAR
	bStart              : BOOL;
	sValue              : STRING(255);
	nPos, nLen          : T_Size;
	tTime               : TIME :=  TIME();
	tTime2              : TIME;
	fbString_Builder    : FB_String_Builder;
	fbRead_Only_SB      : FB_Read_Only_String_Builder_Adapter;
	ipRead_Only_SB      : I_Read_Only_String_Builder_Adapter;
END_VAR
```

**Implementation:**
```ST
fbRead_Only_SB(ipString_Builder := fbString_Builder);
ipRead_Only_SB := fbRead_Only_SB;

IF bStart THEN
	bStart := FALSE;
	fbString_Builder
		.Append('   I love cats.')
		.Append('The time is: ').Append_Any(tTime).Append('.')
		.Trim_Whitespace(T_Whitespace_Location.Both);
	END_IF
	
sValue := ipRead_Only_SB
			.Search('T#', 0, Position => nPos)
			.Copy_Substring_To(nPos, tc2_Standard.LEN(TO_STRING(tTime2)), tTime2)
			.Get_String();
```

**Output:**

![string builder using an adapter](./assets/images/string%20builder%20using%20adapter.png)

---

Working with the enumerator.

**Declarations:** 
```ST
VAR
	bStart : BOOL;
	sValue : STRING(255);
	dtTime : DT :=  DT#2023-5-8-12:55:23;
	fbString_Builder1,
	fbString_Builder2 : FB_String_Builder;
	ipEnumerator : I_Enumerator;
END_VAR
```

**Implementation:**
```ST
IF bStart THEN
	bStart := FALSE;
	
	fbString_Builder1
		.Clear()
		.Append('   I love cats.')
		.Append('The time is: ').Append_Any(dtTime).Append('.')
		.Trim_Whitespace(T_Whitespace_Location.Both);
	
	ipEnumerator := fbString_Builder1.Get_Enumerator();
	WHILE ipEnumerator.Next() DO
		fbString_Builder2.Append(ipEnumerator._Current);
		END_WHILE
		
	sValue := fbString_Builder2.Get_String();
	END_IF
```

**Output:**

![string builder enumerator](./assets/images/string%20builder%20enumerator.png)


# ⚠ Important ⚠ 
This project is still in development. There's a lot of work and testing ahead. Changes to functionality may occur in the future.
