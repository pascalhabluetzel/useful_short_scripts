# useful_short_scripts
A collection of short scripts that do useful stuff for bioinformatics and biodiversity informatics.
Usage: copy and paste script in (Linux) terminal, rename input and output file names and hit enter.

## Convert .fasta to .csv
Python script to be run directly in the shell. It adds column headers 'id' and 'sequence'. Replace ',' by '\t' to obtain a tab-delimited file.

```
fasta_to_csv () {
    PYCMD=$(cat <<EOF
fasta = 'input.fasta'
output = 'output.csv'

out_lines = []
temp_line = ''
with open(fasta, 'r') as fp:
    for line in fp:
        if line.startswith('>'):
            out_lines.append(temp_line)
            temp_line = line.strip() + ','
        else:
            temp_line += line.strip()
out_lines.append(temp_line)

with open(output, 'w') as fp_out:
    fp_out.write('id,sequence' + '\n'.join(out_lines))
EOF
    )

    python3 -c "$PYCMD"
}
fasta_to_csv
```

## Convert .csv to .fasta
Python script to be run directly in the shell.

```
csv_to_fasta () {
    PYCMD=$(cat <<EOF
csv = 'input.csv'
output = 'output.fasta'

out_lines = []
temp_line = ''
with open(csv, 'r') as csv:
    for line in csv:
        cols = line.split(",")
        out_lines.append(temp_line)
        temp_line = ">" + cols[0] + "\n" + cols[1] + "\n"

out_lines.append(temp_line)

with open(output, 'w') as csv_out:
    csv_out.write(''.join(out_lines)[13:])
EOF
    )

    python3 -c "$PYCMD"
}
csv_to_fasta
```

## Convert .fastq to .fasta

```
sed -n '1~4s/^@/>/p;2~4p' input.fastq > output.fasta
```

## Reverse complement sequences in a .fasta file
Only works for sequences that do not contain gaps (-).

```
while read line; do
    if [[ $line =~ ">" ]]; then
        echo $line
    else
        echo $line | tr ACGTRYSWKMBDHVNacgtryswkmbdhvn TGCAYRWSMKVHDBNtgcayrwsmkvhdbn | rev
    fi
done < input.fasta > output.fasta
```

## Merge two tables by common column
```
merge_tables () {
    PYCMD=$(cat <<EOF

import pandas as pd

df_table1 = pd.read_csv("table1.csv", sep=',')
df_table2 = pd.read_csv("table2.csv", sep=',')

combined = pd.merge(df_table1, df_table2, on=['common_column_name'])

combined.to_csv("output.csv", sep=',')

EOF
    )

    python3 -c "$PYCMD"
}
merge_tables
```
