class CarParkPaymentSystem:
    def __init__(self):
        self.daily_total_payments = 0

    def calculate_price(self, day, arrival_hour, num_hours, frequent_parking_number=None):
        # Define prices and discounts
        prices = {
            'Sunday': {'day_price': 2.00, 'evening_price': 2.00},
            'Monday': {'day_price': 10.00, 'evening_price': 2.00},
            'Tuesday': {'day_price': 10.00, 'evening_price': 2.00},
            'Wednesday': {'day_price': 10.00, 'evening_price': 2.00},
            'Thursday': {'day_price': 10.00, 'evening_price': 2.00},
            'Friday': {'day_price': 10.00, 'evening_price': 2.00},
            'Saturday': {'day_price': 3.00, 'evening_price': 2.00}
        }
        discount_percentage = 0.1 if arrival_hour < 16 else 0.5

        # Calculate day price
        day_price = prices[day]['day_price'] * num_hours

        # Calculate discount if applicable
        if frequent_parking_number:
            if self.validate_frequent_parking_number(frequent_parking_number):
                day_price *= (1 - discount_percentage)

        # Calculate evening price if applicable
        evening_price = 0
        if arrival_hour < 16:
            remaining_hours = num_hours - (16 - arrival_hour)
            if remaining_hours > 0:
                evening_price = prices[day]['evening_price'] * remaining_hours

        total_price = day_price + evening_price
        return total_price

    def validate_frequent_parking_number(self, frequent_parking_number):
        # Check if the frequent parking number has correct check digit
        if len(frequent_parking_number) != 5:
            return False
        check_digit = int(frequent_parking_number[-1])
        number = frequent_parking_number[:-1]
        total = sum(int(number[i]) * (len(number) - i) for i in range(len(number)))
        remainder = total % 11
        if remainder == 0:
            calculated_check_digit = 0
        else:
            calculated_check_digit = 11 - remainder
        return check_digit == calculated_check_digit

    def process_payment(self, amount_paid):
        self.daily_total_payments += amount_paid

    def display_daily_total_payments(self):
        print(f"Total payments for the day: {self.daily_total_payments}")


def main():
    car_park = CarParkPaymentSystem()

    # Task 1 - Calculate the price to park
    day = input("Enter the day: ")
    arrival_hour = int(input("Enter the hour of arrival (0-23): "))
    num_hours = int(input("Enter the number of hours to leave the car: "))
    frequent_parking_number = input("Enter frequent parking number if available (5 digits): ")

    price = car_park.calculate_price(day, arrival_hour, num_hours, frequent_parking_number)
    print(f"Amount to pay: ${price:.2f}")

    # Task 2 - Keep a total of payments
    amount_paid = float(input("Enter amount paid: "))
    while amount_paid < price:
        print("Amount paid is less than the required amount. Please enter the correct amount.")
        amount_paid = float(input("Enter amount paid: "))
    car_park.process_payment(amount_paid)

    # Task 3 - Display daily total payments
    car_park.display_daily_total_payments()


if __name__ == "__main__":
    main()
