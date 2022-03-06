# Codecademy-Medical-Insurance-Data---Cummulative-Project

import csv

# Importing dataset from the CSV file provided and saving particular pieces of data into separate lists.

age_lst = []
sex_lst = []
bmi_lst = []
children_lst = []
smoker_lst = []
region_lst = []
charges_lst = []


with open("insurance.csv", newline="") as insurance_csv:
    insurance_reader = csv.DictReader(insurance_csv)
    for row in insurance_reader:
        age_lst.append(row["age"])
        sex_lst.append(row["sex"])
        bmi_lst.append(row["bmi"])
        children_lst.append(row["children"])
        smoker_lst.append(row["smoker"])
        region_lst.append(row["region"])
        charges_lst.append(row["charges"])

# Creating a function which calculates averages of all numerical values in the given list of numerical data 
# (e.g. the function can be used to calculate the average age of the population given, their average BMI, number of children
# and medical insurance charges).

def get_average(lst):
    total_value = 0
    for value in lst:
        total_value += float(value)
    average = total_value / len(lst)
    return average

average_age = get_average(age_lst)
average_bmi = get_average(bmi_lst)
average_children = get_average(children_lst)
average_charges = get_average(charges_lst)

print("""
This medical dataset encompasses data of {len} people. 
The average age of this population is {average_age} years, 
the average BMI is {average_bmi} 
and the average number of children is {average_children}.
The average insurance charges for an individual in this poulation amount to {average_charges}.""".format(len=len(age_lst), average_age=average_age, average_bmi=average_bmi, average_children=average_children, average_charges=average_charges))

# Creating a function which calculates the total number of occurences for different categories within a non-numerical data list (e.g. smokers, regions, genders) and creates the dictionary to present the total values.

def get_total_number_dict(lst):
    data_dict = {}
    for item in lst:
        if item not in data_dict:
            data_dict.update({item: 1})
        else:
            data_dict[item] += 1
    return data_dict

regions_data = get_total_number_dict(region_lst)
sex_data = get_total_number_dict(sex_lst)
smoker_data = get_total_number_dict(smoker_lst)


print("""
The dataset contains medical data of {} women and {} men. 
{} people of the poulation smoke and {} people do not.
The data comes from 4 regions represented by a similar number of people.
The exact numberes are presented below:
""".format(str(sex_data["female"]), str(sex_data["male"]), str(smoker_data["yes"]), str(smoker_data["no"])), regions_data, sex_data, smoker_data)


# Creating a function which checks where the majority of the people live. 
# The function could be particularly useful if the number of regions was higher.

def get_most_repr_region(data_dict):
    max_region = "x"
    max_population = 0
    for key, value in data_dict.items():
        if value > max_population:
            max_region = key
            max_population = value 
    message = """
The majority of people come from {region}. {population} people live there. \n""".format(region=max_region, population=max_population)
    return message
print(get_most_repr_region(regions_data))

# The function defined below creates a dictionary which takes two lists of data, zippes them and returns as a dictionary. 
# The recurrent (non-unique) items from list1 are presented jointly as keys in the dictionary and values are represented by lists
# which contain corresponding items from list2. 
# The dictionary serves comparison purposes, e.g. allows to compare the medical insurance charges of smokers and non-smokers 
# or ages of people who have a particular number of children or do not have any children at all. 

def get_comparison_dict2(list1, list2):
    new_dict = {}
    zipped_list = zip(list1, list2)
    for record in zipped_list:
        if record[0] not in new_dict.keys():
            new_dict.update({record[0]: [record[1]]})
        elif record[0] in new_dict.keys():
            new_dict[record[0]].append(record[1])
    return new_dict
            

smoker_to_charges = get_comparison_dict2(smoker_lst, charges_lst)
children_to_age = get_comparison_dict2(children_lst, age_lst)

# Function calculating averages for values presented in the comparison dictionaries created by calling the function above. 
def present_averages(comparison_dict):
    new_dict = {}
    for key, value_lst in comparison_dict.items():
        total_value = 0
        for value in value_lst:
            total_value += float(value)
        average = total_value / len(value_lst)
        new_dict.update({key: average})
    return new_dict

# Utilising the function above (present_averages) to create the dictionary presenting average values
# for the two different categories: smokers and non-smokers.
# Next, the difference in these averages is calculated. 

smoker_average_charges = present_averages(smoker_to_charges)
print("The following data presents the average charges of smokers and non-smokers. The key is an answer to question \"Do you smoke\"?", smoker_average_charges)

smoker_diff_average_charges = abs(smoker_average_charges["yes"] - smoker_average_charges["no"])
print("Smokers pay on average approx.", int(smoker_diff_average_charges), "dollars more than non-smokers for their medical insurance.")

# Utilising the function "present_averages" to create the dictionary presenting average ages of people 
# with a different number of children

print("""
As stated above, the average age of this population is """ + str(average_age) + """.""")
children_average_age = present_averages(children_to_age)
print("The average age of people in this dataset in relation to the number of children they have can be found in the following dictionary:", children_average_age)  

# The following function creates a dictionary which shows the highest and lowest average age in relation to the number of children.

def get_extreme_averages(average_data_dict):
    max_age_average = 0
    max_average_children = "x"
    min_age_average = float("inf")
    min_average_children = "y"
    for key, value in average_data_dict.items():
        if value > max_age_average:
            max_age_average = value
            max_average_children = key
        elif value < min_age_average:
            min_age_average = value
            min_average_children = key
    new_dict = {max_average_children: max_age_average, min_average_children: min_age_average}
    return new_dict

extreme_averages_children_age = get_extreme_averages(children_average_age)
print("The age average in this dataset is highest for people who have 3 children and lowest for people who have 5 children:", extreme_averages_children_age  )

# Calculating the average age of childless people and parents - utilizing the comparison dictionary "children to age" generated above.

def get_average_age_parents_childless(comparison_dict):
    total_age_parents = 0
    total_age_childless = 0
    for key, value in comparison_dict.items():
        if key == "0":
            for number in value:
                total_age_childless += int(number)
        else:
            for number in value:
                total_age_parents += int(number)
            
    average_age_childless = total_age_childless / len(comparison_dict["0"])
    average_age_parents = total_age_parents / (len(comparison_dict["1"]) + len(comparison_dict["2"]) + len(comparison_dict["3"]) + len(comparison_dict["4"]) + len(comparison_dict["5"]))
    new_dict = {"Average age of childless people": average_age_childless, "Average age of people who have at least one child": average_age_parents}
    return new_dict


average_age_parents_childless = get_average_age_parents_childless(children_to_age)
print("The average ages of people who do and do not have children can be found in the following set:", average_age_parents_childless)
    
