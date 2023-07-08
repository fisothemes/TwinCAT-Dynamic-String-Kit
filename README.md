# TwinCAT Dynamic String Kit

A library that provides tools for working with dynamic strings in TwinCAT. It includes a comprehensive string builder with string enumeration and a read-only string builder adapter. Additionally, there are several C-style string functions, such as `isdigit(...)`, `strstr(...)`, `strlcpy(...)`, `isspace(...)`, and more. There are also functions for converting `ANY` to `STRING` and `STRING` to `ANY`.

# Examples

Working with the string builder.

**Declarations:** 
```ST
VAR
	bStart 				: BOOL;
	nLn1, nLn2 			: T_Size;
	sValue1, sValue2	: STRING(255);
	pString				: POINTER TO STRING;
	fbStringBuilder1,
	fbStringBuilder2	: FB_StringBuilder;
END_VAR
```

**Implementation:**
```ST
IF bStart THEN
	bStart := FALSE;
	fbStringBuilder2
		.Append(' I hate cats.')
		.GetBuffer(pString);
	
	fbStringBuilder1
		.Clear()
		.Append(' I love cats.')
		.Append(' I love dogs.')
		.AppendBuffer(pString)
		.BTrim()
		.GetLength(Length => nLn1)
		.Replace('cats','dogs',TRUE)
		.Insert(nLn1, ' Anaconda! ')
		.Split(nLn1,100, fbStringBuilder2.Append(' Tigers are cats.'))
		.GetLength(Length => nLn1);
	
	sValue1 := fbStringBuilder1.ToUppercase().GetString();
	sValue2 := fbStringBuilder2
		.ToLowercase()
		.BTrim()
		.GetString();
	
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
	bStart				: BOOL;
	sValue 				: STRING(255);
	nPos				: T_Size;
	tTime 				: TIME :=  TIME();
	tTime2				: TIME;
	fbStringBuilder 	: FB_StringBuilder;
	fbReadOnlySB 		: FB_ReadOnlyStringBuilderAdapter;
	ipReadOnlySB 		: I_ReadOnlyStringBuilderAdapter;
END_VAR
```

**Implementation:**
```ST
fbReadOnlySB(ipStringBuilder := fbStringBuilder);
ipReadOnlySB := fbReadOnlySB;

IF bStart THEN
	bStart := FALSE;
	fbStringBuilder
		.Clear()
		.Append('   I love cats.')
		.Append('The time is: ').AppendAny(tTime).Append('.')
		.Append('   ')
		.BTrim();
	END_IF

sValue := ipReadOnlySB
			.Search('T#', 0, Position => nPos)
			.CopySubstringTo(nPos, tc2_Standard.LEN(TO_STRING(tTime)), tTime2)
			.GetString();
```

**Output:**

![string builder using an adapter](./assets/images/string%20builder%20using%20adapter.png)

---

Working with the enumerator.

**Declarations:** 
```ST
VAR
	bStart				: BOOL;
	sValue				: STRING(255);
	dtTime				: DT :=  DT#2023-5-8-12:55:23;
	fbStringBuilder1,
	fbStringBuilder2	: FB_StringBuilder;
	ipEnumerator		: I_Enumerator;
END_VAR
```

**Implementation:**
```ST
IF bStart THEN
	bStart := FALSE;
	
	fbStringBuilder1
		.Clear()
		.Append('   I love cats.')
		.Append('The time is: ').AppendAny(dtTime).Append('.')
		.BTrim();
	
	ipEnumerator := fbStringBuilder1.GetEnumerator();
	WHILE ipEnumerator.Next() DO
		fbStringBuilder2.Append(ipEnumerator.Current);
		END_WHILE
		
	sValue := fbStringBuilder2.GetString();
	END_IF
```

**Output:**

![string builder enumerator](./assets/images/string%20builder%20enumerator.png)


# ⚠ Important ⚠ 
This project is still in development. There's a lot of work and testing ahead. Changes to functionality may occur in the future.
