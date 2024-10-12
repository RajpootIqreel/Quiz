# Quiz
import React, { useState, useEffect } from 'react';
import {
  StyleSheet,
  Text,
  View,
  Button,
  FlatList,
  Alert,
} from 'react-native';

const quizData = [
  {
    question: "What's the capital of France?",
    options: ['Berlin', 'Madrid', 'Paris', 'Lisbon'],
    answer: 'Paris',
  },
  {
    question: "What's the capital of Germany?",
    options: ['Berlin', 'Vienna', 'Brussels', 'Amsterdam'],
    answer: 'Berlin',
  },
  {
    question: "What's the capital of Spain?",
    options: ['Madrid', 'Barcelona', 'Sevilla', 'Valencia'],
    answer: 'Madrid',
  },
  {
    question: "What's the largest ocean on Earth?",
    options: ['Atlantic', 'Indian', 'Arctic', 'Pacific'],
    answer: 'Pacific',
  },
  {
    question: "Which planet is known as the Red Planet?",
    options: ['Earth', 'Mars', 'Jupiter', 'Saturn'],
    answer: 'Mars',
  },
];

export default function App() {
  const [currentQuestionIndex, setCurrentQuestionIndex] = useState(0);
  const [score, setScore] = useState(0);
  const [isQuizFinished, setIsQuizFinished] = useState(false);
  const [timer, setTimer] = useState(30);
  const [intervalId, setIntervalId] = useState(null);

  useEffect(() => {
    if (timer > 0 && !isQuizFinished) {
      const id = setInterval(() => setTimer((prev) => prev - 1), 1000);
      setIntervalId(id);
    } else if (timer === 0) {
      handleNextQuestion();
    }
    return () => clearInterval(intervalId);
  }, [timer, isQuizFinished]);

  const handleAnswer = (selectedOption) => {
    if (selectedOption === quizData[currentQuestionIndex].answer) {
      setScore(score + 1);
    }
    handleNextQuestion();
  };

  const handleNextQuestion = () => {
    if (currentQuestionIndex < quizData.length - 1) {
      setCurrentQuestionIndex(currentQuestionIndex + 1);
      setTimer(30); // Reset timer for next question
    } else {
      setIsQuizFinished(true);
      clearInterval(intervalId);
    }
  };

  const resetQuiz = () => {
    setCurrentQuestionIndex(0);
    setScore(0);
    setIsQuizFinished(false);
    setTimer(30);
  };

  return (
    <View style={styles.container}>
      {!isQuizFinished ? (
        <>
          <Text style={styles.question}>
            {quizData[currentQuestionIndex].question}
          </Text>
          <Text style={styles.timer}>Time Remaining: {timer}s</Text>
          <FlatList
            data={quizData[currentQuestionIndex].options}
            keyExtractor={(item) => item}
            renderItem={({ item }) => (
              <Button title={item} onPress={() => handleAnswer(item)} />
            )}
          />
        </>
      ) : (
        <View style={styles.result}>
          <Text style={styles.resultText}>
            Quiz Finished! Your Score: {score}/{quizData.length}
          </Text>
          <Button title="Restart Quiz" onPress={resetQuiz} />
          <Text style={styles.correctAnswersTitle}>Correct Answers:</Text>
          {quizData.map((question, index) => (
            <Text key={index}>
              {index + 1}. {question.question} - Correct Answer: {question.answer}
            </Text>
          ))}
        </View>
      )}
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  question: {
    fontSize: 20,
    marginBottom: 20,
  },
  timer: {
    fontSize: 16,
    marginBottom: 20,
    color: 'red',
  },
  result: {
    alignItems: 'center',
  },
  resultText: {
    fontSize: 24,
    marginBottom: 20,
  },
  correctAnswersTitle: {
    fontSize: 18,
    fontWeight: 'bold',
    marginTop: 20,
  },
});
