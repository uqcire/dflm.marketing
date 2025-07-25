# Item Code Naming Convention

## Overview
This document defines the unified item coding rules for incoming inventory items, using a constellation-based positioning system to ensure uniqueness, readability, and scalability of item codes.

## Code Structure

### Basic Format
```
[Product Series(3 chars)]-[Constellation Batch(2 chars)]-[Base36 Sequence(4 chars)]
```

### Examples
- `DHY-AZ-A0B1`
- `PST-BV-Z9X2`
- `XLG-CW-M4N7`

### Total Length
- **Without hyphens**: 9 characters
- **With hyphens**: 12 characters

## Code Components Breakdown

### 1. Product Series (3 uppercase letters)
Uses the abbreviation of product name initials, all uppercase.

#### Predefined Product Series Code Table
| Code | Product Name | Description |
|------|--------------|-------------|
| DHY  | Da Huan Dan  | Primary recovery elixirs |
| PST  | Pan Tao      | Celestial fruit products |
| XLG  | Xian Ling Gao| Paste-form products |
| YLQ  | Yu Lu Qiong  | Liquid products |
| TXD  | Tian Xiang Dan| Aromatic elixirs |
| LSW  | Ling Zhi Wan | Pill-form products |

#### New Product Series Coding Rules
- Use initials from product names
- Ensure uniqueness with existing codes
- Recommend memorable combinations

### 2. Constellation Batch (2 uppercase letters)
Uses abbreviations of English constellation names to represent different production lines or production cycles.

#### Constellation Batch Code Table
| Code | English Name     | Chinese Reference | Usage Description |
|------|------------------|-------------------|-------------------|
| AZ   | Azure Dragon     | 青龙              | Production Line A |
| BV   | Black Vermillion | 朱雀              | Production Line B |
| CW   | Celestial White  | 白虎              | Production Line C |
| DT   | Dark Tortoise    | 玄武              | Production Line D |
| ES   | Eastern Star     | 东方星宿          | Special batches |
| FS   | Fire Star        | 火星              | High-temp process |
| GS   | Gold Star        | 金星              | Premium materials |
| HS   | Heaven Star      | 天星              | Limited editions |
| JK   | Jade Kirin       | 玉麒麟            | Premium line |
| LM   | Lunar Moon       | 月亮              | Night shift |
| NP   | Northern Phoenix | 北凤              | Quality control |
| QR   | Quasar Ray       | 类星体            | High-tech process |
| ST   | Silver Tiger     | 银虎              | Standard line |
| UV   | Ursa Vega        | 大熊座            | Bulk production |
| WX   | Wolf Cross       | 狼十字            | Cross-batch |
| YZ   | Zenith Star      | 天顶星            | Final inspection |

#### Batch Code Extension Rules
- Increment alphabetically: AZ → BV → CW → DT...
- Flexible adjustment based on actual production line count
- Reserve specific batch codes for special processes or limited products

### 3. Base36 Sequence Number (4 characters)
Uses Base36 encoding system (0-9, A-Z) to provide high-capacity sequence numbering.

#### Base36 Character Set
```
0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

#### Sequence Number Range
- **Minimum**: `0000`
- **Maximum**: `ZZZZ`
- **Total Capacity**: 1,679,616 numbers

#### Sequence Number Increment Example
```
0000 → 0001 → 0002 → ... → 0009 → 000A → 000B → ... → 000Z → 0010 → 0011 → ...
```

## Code Generation Rules

### Generation Process
1. **Determine Product Series**: Select corresponding 3-character code based on product type
2. **Assign Constellation Batch**: Select 2-character batch code based on production line or special requirements
3. **Generate Sequence Number**: Use next available Base36 sequence number within the product series and batch combination

### Uniqueness Guarantee
- Each product series + constellation batch combination maintains independent sequence number counters
- Sequence numbers strictly increment within the same combination
- Sequence numbers can be reused across different combinations

### Example Generation Process
To generate a code for "Da Huan Dan" product on "Production Line A":
1. Product Series: `DHY`
2. Constellation Batch: `AZ` (Production Line A)
3. Current Sequence: `0001`
4. Complete Code: `DHY-AZ-0001`

Next product code: `DHY-AZ-0002`

## Implementation Recommendations

### Database Design
Recommend maintaining the following table structure in database:

```sql
-- Product Series Table
CREATE TABLE product_series (
    code VARCHAR(3) PRIMARY KEY,
    name VARCHAR(50),
    description TEXT
);

-- Constellation Batch Table
CREATE TABLE constellation_batch (
    code VARCHAR(2) PRIMARY KEY,
    english_name VARCHAR(50),
    chinese_name VARCHAR(20),
    purpose VARCHAR(100)
);

-- Sequence Counter Table
CREATE TABLE sequence_counter (
    series_code VARCHAR(3),
    batch_code VARCHAR(2),
    current_sequence INT DEFAULT 0,
    PRIMARY KEY (series_code, batch_code)
);
```

### Code Generation Function Example (Pseudocode)
```python
def generate_item_code(series_code, batch_code):
    # Get current sequence number and increment
    current_seq = get_and_increment_sequence(series_code, batch_code)
    
    # Convert to Base36 format, pad to 4 characters
    base36_seq = to_base36(current_seq).upper().zfill(4)
    
    # Combine complete code
    full_code = f"{series_code}-{batch_code}-{base36_seq}"
    
    return full_code
```

### Base36 Conversion Tools
For easy viewing of sequence number decimal values, recommend providing conversion tools:

```python
def base36_to_decimal(base36_str):
    return int(base36_str, 36)

def decimal_to_base36(decimal_num):
    if decimal_num == 0:
        return "0"
    
    chars = "0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    result = ""
    
    while decimal_num > 0:
        result = chars[decimal_num % 36] + result
        decimal_num //= 36
    
    return result
```

## Maintenance and Extension

### Adding New Product Series
1. Add new 3-character code to product series code table
2. Ensure code uniqueness
3. Initialize counters for new series in sequence counter table

### Adding New Constellation Batches
1. Add new 2-character code to constellation batch code table
2. Initialize corresponding sequence counters for related product series

### Code Format Changes
When changing code format, recommend:
1. Maintain backward compatibility
2. Develop migration plan
3. Update related systems and documentation

## Important Notes

### Character Restrictions
- Avoid easily confused character combinations
- Recommend using monospace fonts for display to improve readability

### Backup and Recovery
- Regularly backup sequence counter states
- Develop solutions for sequence number conflicts

### Performance Considerations
- Base36 conversion may impact performance with large amounts of data
- Recommend storing decimal sequence numbers in database for sorting purposes

---

**Document Version**: 1.0  
**Created**: October 2024  
**Last Updated**: October 2024 