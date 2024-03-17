import ipywidgets as widgets
from IPython.display import HTML
import math

loan_type_options = ['Sub/Unsub Loan', 'PPLUS Loan']
loan_type_dropdown = widgets.Dropdown(options=loan_type_options, description='Loan Type')
display(loan_type_dropdown)

loan_amount_input = widgets.Text(value='', placeholder='Enter loan amount', description='Loan Amount:')
display(loan_amount_input)

output_info = widgets.Output()
display(output_info)

def calculate_origination_fee(loan_amount, fee_percentage):
    origination_fee = fee_percentage / 100 * float(loan_amount)
    return math.ceil(origination_fee)

def on_loan_amount_change(change):
    global loan_type_dropdown

    loan_amount = change.new.strip()
    if loan_amount:
        loan_type = loan_type_dropdown.value
        if loan_type == 'PPLUS Loan':
            fee_percentage = 4.228
        elif loan_type == 'Sub/Unsub Loan':
            fee_percentage = 1.057
        else:
            fee_percentage = 0

        origination_fee = calculate_origination_fee(loan_amount, fee_percentage)
        loan_amount_after_fee = float(loan_amount) - origination_fee
        loan_amount_offset_fee = float(loan_amount) + origination_fee

        with output_info:
            output_info.clear_output()
            display(HTML('<span style="font-size: 16pt;">The origination fee percentage is: {:.3f}%</span>'.format(fee_percentage)))
            display(HTML('<span style="font-size: 16pt;">The origination fee is: ${}</span>'.format(origination_fee)))
            display(HTML('<span style="font-size: 16pt;">Loan amount after origination fee applied: ${}</span>'.format(int(loan_amount_after_fee))))
            display(HTML('<span style="font-size: 16pt;">Loan amount needed to offset origination fee: ${}</span>'.format(int(loan_amount_offset_fee))))
    else:
        output_info.clear_output()
        print("Please enter a valid loan amount.")

loan_amount_input.observe(on_loan_amount_change, names='value')

def on_loan_type_change(change):
    loan_amount = loan_amount_input.value
    if loan_amount:
        on_loan_amount_change({'new': loan_amount})

loan_type_dropdown.observe(on_loan_type_change, names='value')
