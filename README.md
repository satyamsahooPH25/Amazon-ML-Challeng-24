# Amazon Product Attribute Extraction System
![Architecture](https://github.com/user-attachments/assets/92e82681-f450-4cd8-b5b3-7405a4a72fef)

This repository contains a robust system for extracting and predicting key product attributes such as weight, volume, length, voltage, wattage, etc., from product images using Optical Character Recognition (OCR) based on given query. The system efficiently predicts the correct units and numerical values associated with these attributes, providing a scalable solution for automated product data extraction.

## Problem Statement

The dataset, provided by Amazon, includes product images containing information such as weight, length, volume, voltage, wattage, etc., with varying units within same query ,ex weight-(kg, gm, mg, pounds). The challenge is to extract this information from the images based on given query and accurately predict the correct units and numerical values.

## Approach

The system follows a multi-stage approach:

### 1. OCR for Text Extraction
- EasyOCR is used to extract text from the product images. The extracted text includes a mixture of numeric values and units (e.g., "15.2 cm", "250 gm").

### 2. Text Cleaning and Preprocessing
- The extracted text is cleaned and split into two sequences:
  - Unit Sequence: Extracts the units (e.g., cm, gm, mg, etc.).
  - Value Sequence: Extracts the corresponding numerical values (e.g., 15.2, 250).

### 3. Unit Prediction with LSTM Encoder-Decoder
- LSTM Encoder: Takes in the TF-IDF embeddings of the cleaned text sequence.
- LSTM Decoder: Uses one-hot encoded sequences of units to predict the correct unit associated with each product.
- Softmax: The output of the LSTM decoder is passed through a softmax layer to finalize the prediction of the unit.

### 4. Attention Mechanism for Unit-Value Association
- Bahdanau Attention: Applied to identify the most contributing unit within the sequence. The attention score is calculated for each unit.
- Unit with Highest Attention Score: The numerical value corresponding to the unit with the maximum attention weight is chosen.

### 5. Numerical Value Prediction using GRU Encoder-Decoder and Se;f-Attention
- To ensure robustness in selecting the correct numerical value, we represent the correct value as a sequence (1 at the correct index and 0 elsewhere).
- GRU Encoder: Takes the attention weight sequence as input.
- GRU Decoder: Outputs the correct sequence (a sequence of zeroes and ones), where '1' indicates the correct index of the numerical value.
- This step ensures that even if the unit prediction is slightly off, the system can still identify the correct numerical value.

## Model Architecture

The model integrates multiple components to ensure high accuracy:
- LSTM Encoder-Decoder: For unit prediction.
- Bahdanau Attention: To associate units with their respective numerical values.
- GRU Encoder-Decoder: For robust numerical value prediction using attention scores.

## System Flow

1. Text Extraction: OCR is used to extract text from the product images.
2. Text Cleaning: The extracted text is split into units and values.
3. Unit Prediction: Using an LSTM Encoder-Decoder network, the correct unit is predicted from the cleaned text sequence.
4. Attention Scoring: Attention scores are computed to associate units with their respective numerical values.
5. Numerical Value Prediction: A GRU-based Encoder-Decoder system refines the prediction of the correct numerical value, ensuring robustness.

## Example Workflow

1. Input Image: A product image contains the text "15.2 cm, 250 gm".
2. OCR Output: Extracts "15.2 cm, 250 gm".
3. Text Cleaning: Separates into sequences [15.2, 250] for values and [cm, gm] for units.
4. LSTM Prediction: Predicts the unit 'gm' for the product.
5. Attention Mechanism: Identifies that 'gm' has the highest attention score.
6. GRU Prediction: Predicts that '250' is the correct numerical value for 'gm'.
