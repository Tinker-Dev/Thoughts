# Locations within a List

```
def get_index_positions(list_of_elems, element):
	''' Returns the indexes of all occurrences of give element in
	the list- listOfElements '''
	index_pos_list = []
	index_pos = 0
	while True:
		try:
			# Search for item in list from indexPos to the end of list
			index_pos = list_of_elems.index(element, index_pos)
			# Add the index position in list
			index_pos_list.append(index_pos)
			index_pos += 1
		except ValueError as e:
			break
	return index_pos_list

zero = 0
selectedyears = [gv_Year_1, gv_Year_2, gv_Year_3, gv_Year_4]

forward_index_pos = selectedyears.index(zero)
reverse_index_pos = len(selectedyears) - selectedyears[::-1].index(zero) - 1
index_pos_list = get_index_positions(selectedyears, zero)

print(f'First Index of element "{zero}" in the list : ', forward_index_pos)
print(f'Last Index of element "{zero}" in the list : ', reverse_index_pos)
print(f'Indexes of all occurrences of "{zero}" in the list are : ', index_pos_list)

```