# Cancer Classification Using Deep Learning (TF + Keras)

This project implements a deep neural network (DNN) for binary classification using TensorFlow and Keras. The goal is to predict whether a cancer tumor is malignant (M) or benign (B) based on various features extracted from medical data.

## Overview

- **Objective**: Predict the malignancy of tumors (M/B) based on features from the dataset.
- **Dataset**: The dataset consists of various numerical features about cancer cells, and the target variable indicates if the tumor is malignant or benign.
- **Model**: We used a deep neural network with multiple layers (Dense) and regularization techniques (Dropout, EarlyStopping) to improve performance.
- **Tools Used**:
  - TensorFlow (Keras) for building and training the model.
  - Scikit-learn for data preprocessing and splitting.
  - Pandas and NumPy for data manipulation.
  - Matplotlib and Seaborn for data visualization.

## Requirements

To run this project, you will need the following libraries:

- TensorFlow
- Keras
- Scikit-learn
- Pandas
- NumPy
- Matplotlib
- Seaborn

You can install them using pip:

```bash
pip install tensorflow scikit-learn pandas numpy matplotlib seaborn
```

## Dataset

The dataset used for this project is the **Cancer Classification** dataset, where the goal is to predict whether a tumor is malignant (M) or benign (B). The dataset includes features such as:
- Radius, texture, perimeter, area, smoothness, etc.

You can replace the `cancer_classification.csv` with your dataset, but ensure the data has the same structure.

## Project Structure

```
.
├── cancer_classification.csv  # Dataset
├── cancer_classification.py   # Python script with model code
└── README.md                  # Project description
```

## Steps

### 1. **Load and Preprocess the Data**

The dataset is loaded using `pandas.read_csv()` and preprocessed for training.

```python
df = pd.read_csv('dl_datasets_1/cancer_classification.csv')
X = df.iloc[:, :-1].values  # Feature set
y = df.iloc[:, -1].values   # Target variable (M/B)

# Split data into training and testing sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.33, random_state=42)

# Scale the data using MinMaxScaler
sc = MinMaxScaler()
X_train = sc.fit_transform(X_train)
X_test = sc.transform(X_test)
```

### 2. **Build the Deep Learning Model**

We start by building a simple neural network using Keras' Sequential API.

#### Model 1: Basic Neural Network
```python
model = models.Sequential()
model.add(layers.Dense(units=30, activation='relu'))
model.add(layers.Dense(units=15, activation='relu'))
model.add(layers.Dense(units=1, activation='sigmoid'))  # Output layer for binary classification

model.compile(optimizer='adam', loss='binary_crossentropy')

model.fit(x=X_train, y=y_train, epochs=600, validation_data=(X_test, y_test))
```

### 3. **Add EarlyStopping to Prevent Overfitting**

To prevent overfitting, we added an `EarlyStopping` callback, which stops training if the validation loss does not improve after a specified number of epochs.

```python
early_stop = EarlyStopping(monitor='val_loss', mode='min', patience=25)

model.fit(x=X_train, y=y_train, epochs=600, validation_data=(X_test, y_test),
          callbacks=early_stop, batch_size=32)
```

### 4. **Add Dropout for Regularization**

To further prevent overfitting, we added dropout layers after each hidden layer. Dropout randomly sets some fraction of input units to 0 during training, which helps regularize the model.

```python
model = models.Sequential()
model.add(layers.Dense(units=30, activation='relu'))
model.add(layers.Dropout(0.5))
model.add(layers.Dense(units=15, activation='relu'))
model.add(layers.Dropout(0.5))

model.add(layers.Dense(units=1, activation='sigmoid'))  # Output layer for binary classification
model.compile(optimizer='adam', loss='binary_crossentropy')

early_stop = EarlyStopping(monitor='val_loss', mode='min', patience=25)
model.fit(x=X_train, y=y_train, epochs=600, validation_data=(X_test, y_test),
          callbacks=early_stop, batch_size=32)
```

### 5. **Model Evaluation**

After training the model, we evaluate its performance using training and testing datasets.

```python
training_loss = model.evaluate(X_train, y_train)
testing_loss = model.evaluate(X_test, y_test)
print(f"Training loss: {training_loss}")
print(f"Testing loss: {testing_loss}")
```

### 6. **Make Predictions**

Once the model is trained, we can use it to make predictions on the test data.

```python
test_prediction = model.predict(X_test)
test_prediction = pd.Series(test_prediction.reshape(188,))
pred_data = pd.DataFrame(y_test, columns=['Y True value'])
pred_data = pd.concat([pred_data, test_prediction], axis=1)
pred_data.columns = ['Y True value', 'Y predicted value']
```

### 7. **Visualize Model Loss**

We visualize the training and validation loss over epochs to monitor the model's performance during training.

```python
loss_df = pd.DataFrame(model.history.history)
loss_df.plot()
```

## Results

The model achieves a relatively low loss on both the training and testing sets, indicating that it is able to predict the tumor type (malignant or benign) accurately.

## Conclusion

This project demonstrates how to use deep learning techniques (DNN) to classify cancer data using TensorFlow and Keras. Various techniques such as early stopping, dropout, and scaling were applied to improve the model's performance and prevent overfitting.

## Future Improvements

- Explore using more advanced architectures like Convolutional Neural Networks (CNNs) or Recurrent Neural Networks (RNNs) for time-series or image data.
- Perform hyperparameter tuning to optimize the model's performance.

---

Feel free to modify the `README.md` to better match your project's specific details, including how to run it, dataset info, and additional instructions!
