# The Azure Stay Hotel Revenue Leakage Management

## Background & Pain Points
### Scenario:
The Hotel Business Owner's Dilemma
### Context:
You own a mid-sized, independent hotel ("The Azure Stay"). While the property is beautiful, profitability is stagnant. You have identified three critical pain points that require data-driven solutions:
### Pain Points (Problem):
Revenue Leakage (Cancellations & No-Shows): Your calendar looks full two weeks out, but by the time the date arrives, you have empty rooms due to last-minute cancellations and no-shows that you can't resell.
### Goal:
Reduce revenue loss caused by fake or unstable demand.

## Smart Objectives
```
เพื่อกู้คืนรายได้ที่สูญเสียไป อย่างน้อย 15% ภายในระยะเวลา 2 ไตรมาสเมื่อเทียบกับไตรมาสก่อนหน้า
โดยอาศัยการวิเคราะห์ข้อมูลพฤติกรรมการยกเลิกการจอง เพื่อช่วยให้โรงแรมสามารถคัดเลือกลูกค้าที่มีความน่าเชื่อถือได้อย่างเหมาะสม
```
## Hypothesis

1. ถึงจะมีจำนวนการ Cancel เยอะกว่ามาก แต่ No-Show สร้างความเสียหายสุทธิมากกว่า
2. การจองผ่านช่องทาง OTA อื่นๆทำให้มีการ Cancel และ No-Show มากกว่าจองตรงกับโรงแรม
3. การยกเลิกส่วนใหญ่เกิดจากการจองที่มี Policy แบบ Flexible แต่สามารถใช้ Deposit สูงๆกดอัตราการยกเลิกให้ต่ำลงได้
4. อัตราการยกเลิกจะพุ่งสูงขึ้นอย่างผิดปกติในช่วงช่วงเทศกาล หรือตามฤดูกาลต่างๆ
5. ลูกค้าต่างทวีปมีพฤติกรรมการยกเลิกที่แตกต่างจากลูกค้าในประเทศ
6. การยกเลิกส่วนใหญ่มักเกิดขึ้นซ้ำๆจากลูกค้ากลุ่มเดิมๆ (Serial Cancellers)
7. ยิ่งจองล่วงหน้านาน (Long Lead Time) โอกาสยกเลิกยิ่งสูง และมักจะกดยกเลิกใกล้กับวันเข้าพัก
8. No-Show มักเกิดขึ้นกระจุกตัวในวันหยุดสุดสัปดาห์ (ศุกร์-อาทิตย์)
9. ค่าปรับ (Penalty) ที่ได้คืนมา สร้างรายได้ได้ต่ำกว่าเป้าหมาย โดยเฉพาะจากการจองแบบ Flexible ผ่าน OTA

## Prompt used for generating data
ในการ generate dataset ชุดนี้ขึ้นมาใช้ทั้งหมด 2 prompt ตามลำดับดังนี้

1. First propmt
```
ตอนนี้ฉันกำลังทำ Project วิชา Data Analytics and Business Intelligence สำหรับการทำ Project วิชานี้

ฉันต้องการให้คุณ generate prompt สำหรับสร้าง dataset ในรูปแบบของไฟล์ csv ภายใต้บริบทดังนี้

Scenario: The Hotel Business Owner's Dilemma
Context: You own a mid-sized, independent hotel ("The Azure Stay"). While the property is beautiful, profitability is stagnant. You have identified three critical pain points that require data-driven solutions:

Problem : Revenue Leakage (Cancellations & No-Shows): Your calendar looks full two weeks out, but by the time the date arrives, you have empty rooms due to last-minute cancellations and no-shows that you can't resell.

Goal: Reduce revenue loss caused by fake or unstable demand.

Smart Objective : เพื่อกู้คืนรายได้ที่สูญเสียไป อย่างน้อย 15% ภายในระยะเวลา 2 ไตรมาสเมื่อเทียบกับ ไตรมาสก่อนหน้า โดยอาศัยการวิเคราะห์ข้อมูลพฤติกรรมการยกเลิกการจอง เพื่อช่วยให้โรงแรมสามารถคัดเลือกลูกค้าที่มีความน่าเชื่อถือได้อย่างเหมาะสม

Measures & Dimensions 
These are the quantitative metrics used to evaluate performance.

1. ADR (Average Daily Rate)
Definiiton: The value of the room. Used here to calculate Revenue Opportunity Lost.
Calculation Formula: Total Room Revenue / Number of Rooms Sold
Range of Values: $0 - Rack Rate

2. BLT (Booking Lead Time)
Definition: The number of days between the booking date and the arrival date
Calculation Formula: Arrival Date - Booking Date
Range of Values: 0 - 365+ days

3. Commission Cost
Definition: The actual dollar amount paid to the 3rd party.
Calculation Formula: Gross Revenue * Commission Rate (or flat fee)

4. Cancellation Rate
Definition: % of confirmed bookings that cancel before arrival.
Calculation Formula: (Cancelled Bookings / Total Bookings) * 100

5. No-Show Rate
Definition: % of guests who don't arrive and don't cancel.
Calculation Formula: (No Shows / Expected Arrivals) * 100

6. Revenue Lost
Definiton: The total value of rooms that were cancelled and not re-sold (or sold at a lower rate).
Calculation Formula: Sum(Cancelled Booking Value)

7. Cancellation Penalty Collected
Definition: Revenue retained from non-refundable bookings or late cancel fees.
Calculation Formula: Sum(Penalty Fees Charged)

Key Dimensions
These are the categorical attributes used to slice and dice the measures.

1. Booking Channel
Definition: Source of booking. (OTAs often have higher cancel rates).
Set of Possible Values: Direct Website, OTA (Expedia, Booking.com), Walk-in, Corporate Agent, GDS

2. Rate Code
Definition: The specific price package or discount applied to the reservation.
Set of Possible Values: Rack Rate, AAA Discount, Corporate Negotiated, Non-Refundable, Seasonal Promo

3. Day of Week
Definition: The specific day the room is occupied (critical for weekend vs. weekday analysis).
Set of Possible Values: Mon, Tue, Wed, Thu, Fri, Sat, Sun

4. Cancellation Reason
Definition: Why the guest cancelled (if known).
Set of Possible Values: Found Cheaper Price, Change of Plans, Weather, Double Booking, Unknown

5. Policy Type
Definiton: The strictness of the booking rules.
Set of Possible Values: Fully Flexible, 48h Cancellation, Non-Refundable

6. Guest History Status
Definition: Guest's past behavior.
Set of Possible Values: First Time, Repeat Loyal, Repeat Serial Canceller

7. Booking Window Bucket
Definition: Grouping of Lead Time for easier analysis.
Set of Possible Values: Last Minute (0-3 days), Short (4-14), Medium (15-60), Long (60+)

Sample Dataset

Table 1: fact_bookings (Enhanced Transaction Data)
The central table. Note the addition of specific timestamp columns to track the "lifespan" of a booking.
- booking_id (PK): Unique ID.
- guest_id (FK): Links to dim_guests.
- booking_date: Date created.
- check_in_date: Scheduled arrival.
- channel_id (FK): Links to dim_channels.
- policy_id (FK): (New) Links to dim_policies (defines the rules for this specific booking).
- status: Confirmed, Cancelled, No-Show, Checked-In.
- cancellation_date: (New) The date the cancellation occurred (NULL if not cancelled).
- cancellation_reason_code: (New) Code indicating why (e.g., RSN_01).
- gross_revenue: The potential revenue.
- penalty_charged: (New) Amount charged despite cancellation (e.g., 1st night fee).
- deposit_paid: (New) Boolean or Amount. Did they put money down? (High deposit = Low cancel risk).

Table 2: dim_policies (The Rules)
This table helps you analyze if your policies are too loose or too strict.
- policy_id (PK): Unique ID (e.g., POL_FLEX_24).
- policy_name: Name (e.g., "24 Hour Flexible").
- cancellation_deadline_hours: Hours before arrival they must cancel by (e.g., 24, 72, 168).
- penalty_type: First Night, Full Stay, None.
- is_refundable: Boolean (True/False).

Table 3: dim_guests (Guest Behavior Profile)
Crucial for identifying "Serial Cancellers" (people who book and cancel repeatedly).
- guest_id (PK): Unique ID.
- total_bookings_lifetime: Count of all bookings ever made.
- total_cancellations_lifetime: Count of cancellations.
- cancellation_ratio: Calculated field (total_cancellations / total_bookings).
- country_of_origin: Where they are from (some regions have higher cancel rates).

Table 4: dim_calendar
A standard date dimension to make "Day of Week" analysis easier.
- date_key: YYYY-MM-DD.
- day_name: Mon, Tue...
- event_flag: Is there a major concert/event in town? (Events often cause "fake" bookings where people reserve rooms just in case).

Table 5: dim_reason_cancellation
A table for indentifying cancellation codes
- cancellation_reason_code: Code indicating why (e.g., RSN_01).
- reasons: Reaons of cancellation of each code (Change of Plans, Found Cheaper, Flight/Weather, Medical/Emergency, Visa Issues, Unknown)

Project Evaluation Rubric (Total 100 Points)

Section 1: Strategic Alignment (15 Points)
This section evaluates how well the group understands the business context and defines success.
1.1
Criteria: Background & Pain Points
Points: 7
Description of Excellence: Clearly articulates the "Azure Stay" dilemma. Correctly identifies the specific problem chosen (Revenue Stagnation, High Costs, or Leakage) and its impact on profitability.

1.2
Criteria: SMART Objectives
Points: 8
Description of Excellence: Objectives are Specific, Measurable, Achievable, Relevant, and Time-bound. They align with the goals mentioned in the scenario (e.g., maximizing Net RevPAR or reducing No-Shows).

Section 2: Analytical Design (25 Points)
This section evaluates the logic used to set up the data investigation.
2.1
Criteria: Hypothesis & Method
Points: 10
Description of Excellence: Develops testable 3 hypotheses and correctly applies formulas (e.g., Net ADR or Cancellation Rate).

2.2
Criteria: GitHub Documentation
Points: 15
Description of Excellence: The repository is professionally organized with a clear README.md explaining the project. Includes code scripts, the AI-generated dataset, and a logical folder structure. Commit history shows consistent progress.  Set it as a public repository.  Thai or English is acceptable.

Section 3: Data Execution & EDA (30 Points)
This section focuses on the technical application and the quality of the AI-generated dataset.
3.1
Criteria: AI Data Quality 
Points: 10
Description of Excellence: AI-generated data strictly adheres to the required schema (e.g., fact_bookings, dim_channels, dim_guests).  Declare your prompt that is used for generating data.  Declare definitions and descriptions.  For any measure, declare how to calculate.  For any dimension derived from a conditional statement, declare it.  

3.2
Criteria: EDA & Visualizations
Points: 20
Description of Excellence: Explain clearly what you want to explore in EDA.  Uses high-quality charts to analyze measures by critical dimensions like Booking Channel, Rate Code, or Policy Type.  Every chart must have your explanation why it is an appropriate choice.

Section 4: Insights & Impact (15 Points)
This section evaluates the "Business Intelligence" aspect—turning data into value.
4.1
Criteria: Findings (Insights)
Points: 7
Description of Excellence: Accurately interprets results to identify the "root cause" of the problem (e.g., identifying "Serial Cancellers").

4.2
Criteria: Recommendations
Points: 8
Description of Excellence: Provides actionable advice, such as optimizing marketing spend for high-profit channels or adjusting penalty types.

Section 5: Communication & Presentation (15 Points)
This section evaluates the live delivery and professional quality.
5.1
Criteria: Live Presentation
Points: 10
Description of Excellence: Professional delivery, clear slides, and effective storytelling. The group handles Q&A with confidence, demonstrating a deep understanding of their data and the hotel's business logic.

5.2
Criteria: Participant Questioning
Points: 5
Description of Excellence: Group members participate mastery by contributing on questioning to the presenters. Every group representative (participant side) contributes to defending the analytical choices.  For each group presentation, timing is set at 10 minutes (7 minutes for presentation and 3 minutes for Q&A).  Questions must reflect your understanding in analytics.

Dataset ที่ฉันต้องการให้คุณ Generate นั้นต้องมี Data Quality Rules
1. Completeness ไม่ต้องมาทำขั้นตอน Data cleaning อีกเลย
2. Consistency (ความสอดคล้อง)
3. พฤติกรรมของลูกค้าสมเหตุสมผลตามโลกจริง

โดยต้องการข้อมูลอย่างน้อย 10,000 records

แพทเทิร์นที่ต้องการให้ซ่อนไว้ (Embedded Insights & Patterns)
ภายใน Dataset ต้องมีข้อมูลที่มี insights & patterns สอดคล้องกับ Objective โดยไม่ต้องบอกฉัน แต่ฉันต้องสามารถที่จะหา insights & patterns ได้จากกระบวนการทำ EDA
```

2. Second propmt
```
Role: Act as a Senior Data Engineer and Hotel Revenue Manager.

Context: You are tasked with generating a realistic dataset for a mid-sized independent hotel ("The Azure Stay") to analyze Revenue Leakage caused by cancellations and no-shows. The objective is to reduce revenue loss by at least 15% over 2 quarters.

Instructions:
Generate a comprehensive relational dataset in a spreadsheet format with exactly 10,000 records in the fact table covering the period from January 2024 to June 2025. The dataset must strictly adhere to Data Quality rules: 100% Completeness (no missing values unless logically required, e.g., cancellation_date for checked-in guests) and 100% Consistency.

Schema Requirements:
1. fact_bookings: booking_id (PK), guest_id (FK), booking_date, check_in_date, lead_time_days, length_of_stay, channel_id (FK), policy_id (FK), status (Checked-In, Cancelled, No-Show), cancellation_date, cancellation_reason_code, adr_usd, gross_revenue_usd, deposit_paid_usd, penalty_charged_usd.
2. dim_policies: policy_id (PK), policy_name, cancellation_deadline_hours, penalty_type, is_refundable.
3. dim_channels: channel_id (PK), channel_name, commission_rate.
4. dim_guests: guest_id (PK), country_of_origin, total_bookings_lifetime, total_cancellations_lifetime, cancellation_ratio. (Ensure aggregate fields align perfectly with fact_bookings).
5. dim_calendar: date_key (PK), day_of_week, month, is_weekend, event_flag.
6. dim_reason_cancellation: cancellation_reason_code (PK) [RSN_01-RSN_06], reasons [Found Cheaper Price, Change of Plans, Flight/Weather, Visa Issues, Medical/Emergency, Unknown]

Behavioral Rules & Embedded Insights (Do not explicitly document these outputs, but embed them in data generation):
- Create a realistic distribution of booking channels (e.g., OTAs, Direct, Walk-in).
- Inject logical correlations: specific combinations of booking channels and lenient policies must have disproportionately high cancellation rates.
- Simulate "Serial Cancellers": A small percentage of guests (e.g., 5%) should be responsible for a large volume of cancellations, booking well in advance and cancelling just before deadlines.
- Simulate "Event Fake Demand": When event_flag = 1 in dim_calendar, there should be a spike in bookings with flexible policies that end up cancelling.
- Calculate penalty_charged_usd dynamically based on the difference between cancellation_date, check_in_date, and the policy rules in dim_policies.
```

## Promt สำหรับทำ eda + visualization
```
You are an Expert Data Scientist and Revenue Manager for a hotel. I have a dataset for "The Azure Stay" hotel. 
IMPORTANT: DO NOT generate any synthetic data or mock data. Assume the 6 CSV files already exist in the working directory.

Your task is to programmatically GENERATE a complete, ready-to-use Jupyter Notebook file named `Azure_Hotel_Project.ipynb`. 
Do NOT output the notebook content as plain text with tags like [Markdown Cell] or [Python Code Cell] for me to copy-paste. Instead, you MUST write and execute a Python script using the `nbformat` library to construct the notebook cells, save it as `Azure_Hotel_Project.ipynb`, and provide me with a downloadable link.

**Data Dictionary (Use these EXACT column names):**
1. `fact_bookings.csv`: booking_id, guest_id, booking_date, check_in_date, lead_time_days, length_of_stay, channel_id, policy_id, status (Checked-In, Cancelled, No-Show), cancellation_date, cancellation_reason_code, adr_usd, gross_revenue_usd, deposit_paid_usd, penalty_charged_usd
2. `dim_guests.csv`: guest_id, country_of_origin, total_bookings_lifetime, total_cancellations_lifetime, cancellation_ratio
3. `dim_channels.csv`: channel_id, channel_name, commission_rate
4. `dim_policies.csv`: policy_id, policy_name, cancellation_deadline_hours, penalty_type, is_refundable
5. `dim_calendar.csv`: date_key, day_of_week, month, is_weekend, event_flag
6. `dim_reason_cancellation.csv`: cancellation_reason_code, reason

**Structure of the generated Notebook:**

**Part 1: Data Loading & Preparation**
- [Code Cell]: Include code to load the 6 CSV files (using pandas) and merge the `fact_bookings` table with all dimensions appropriately.
- [Code Cell]: Convert date columns to datetime objects. Calculate a new feature: `cancellation_window_days` (difference between `check_in_date` and `cancellation_date`). Note: `lead_time_days` is already provided.

**Part 2: Hypothesis Testing & EDA**
For EACH of the following 9 hypotheses, create these consecutive cells in the notebook:
1. **[Markdown Cell]:** State the Hypothesis in Thai and explain the Formula/Calculation used to test it.
2. **[Code Cell]:** Clean, well-commented Python code to prepare the data, perform calculations, and plot the visualization using `matplotlib` and `seaborn`.
3. **[Markdown Cell]:** - **Chart Justification:** Explain in Thai WHY this specific chart type is the most appropriate.
   - **Findings & Root Cause:** Accurately interpret the (expected) results in Thai to identify the root cause of the problem.

**The 9 Hypotheses to test:**
1. Although the volume of 'Cancelled' is higher, 'No-Show' creates a higher Net Revenue Lost (gross_revenue_usd - penalty_charged_usd).
2. Bookings via OTA channels have significantly higher Cancellation and No-Show rates compared to Direct Website bookings.
3. The majority of cancellations come from 'Flexible' policies, but higher `deposit_paid_usd` amounts correlate with lower cancellation rates.
4. Cancellation rates spike abnormally during Event days (`event_flag` == 1).
5. Guests from different continents/regions (`country_of_origin`) exhibit different cancellation behaviors compared to domestic guests.
6. A large portion of cancellations is repeatedly caused by the same group of guests ("Serial Cancellers" based on `cancellation_ratio`).
7. Long Lead Time (`lead_time_days`) bookings have a higher probability of cancellation, and these guests tend to cancel very close to the check-in date (`cancellation_window_days`).
8. No-Show behaviors are heavily concentrated on weekends (`is_weekend` == True).
9. The Cancellation Penalty collected (`penalty_charged_usd`) falls extremely short of recovering the Revenue Lost (`gross_revenue_usd`), especially from Flexible policies booked via OTAs.

**Part 3: Business Recommendations & Impact**
- **[Markdown Cell]:** Provide 3-4 highly actionable pieces of advice (Recommendations) in Thai based on the findings from Part 2. 
- Ensure these recommendations address:
  - Optimizing marketing spend for high-profit/low-risk channels.
  - Adjusting policy types or strictness (e.g., penalty rules, deposit requirements).
  - Strategies to deal with Serial Cancellers or Long Lead Time risks.
- Tie the recommendations back to the SMART objective: "Recovering at least 15% of lost revenue in the next 2 quarters."

**Execution Rules:**
- Use `nbformat.v4` to create `new_markdown_cell` and `new_code_cell`.
- Write the Python code robustly, handling potential Null values (e.g., `cancellation_date` is null for 'Checked-In' status).
- Output the explanations, Markdown text, and findings inside the notebook in professional Thai language (except for variable names in code).
- Execute the script, generate the `.ipynb` file, and provide the download link.
```
