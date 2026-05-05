---
name: cross-modal-agent-synthesis
version: 1.0.0
description: Agent skill for cross-modal synthesis - translating between text, image, audio, video, code, and other modalities with unified understanding and generation
author: luo-kai
tags: [cross-modal, multimodal, synthesis, translation, unified]
---

# Cross-Modal Agent Synthesis

## Before Starting
1. What source modality is being converted?
2. What target modality is needed?
3. What semantic content must be preserved?
4. What style/quality requirements exist?

You are a cross-modal synthesis agent that translates between different modalities while preserving semantic meaning. You handle text, image, audio, video, code, and structured data with unified understanding.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│              Cross-Modal Synthesis System                       │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐            │
│  │  Text   │  │  Image  │  │  Audio  │  │  Video  │            │
│  │ Encoder │  │ Encoder │  │ Encoder │  │ Encoder │            │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘            │
│       │            │            │            │                  │
│       ▼            ▼            ▼            ▼                  │
│  ┌──────────────────────────────────────────────────────┐      │
│  │              Unified Semantic Space                  │      │
│  │  • Modality-agnostic representations               │      │
│  │  • Cross-modal alignment                            │      │
│  │  • Semantic preservation                            │      │
│  └──────────────────────────────────────────────────────┘      │
│         │                                                       │
│         ▼                                                       │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐            │
│  │  Text   │  │  Image  │  │  Audio  │  │  Video  │            │
│  │ Decoder │  │ Decoder │  │ Decoder │  │ Decoder │            │
│  └─────────┘  └─────────┘  └─────────┘  └─────────┘            │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Modality Encoders

```python
from abc import ABC, abstractmethod
from dataclasses import dataclass
from typing import Dict, Any, Optional
import numpy as np

@dataclass
class ModalityType:
    TEXT = "text"
    IMAGE = "image"
    AUDIO = "audio"
    VIDEO = "video"
    CODE = "code"
    STRUCTURED = "structured"
    THREE_D = "3d"

class ModalityEncoder(ABC):
    """Base encoder for modalities."""
    
    @abstractmethod
    def encode(self, content: Any) -> np.ndarray:
        """Encode content to unified space."""
        pass
    
    @abstractmethod
    def decode(self, embedding: np.ndarray) -> Any:
        """Decode from unified space."""
        pass

class TextEncoder(ModalityEncoder):
    """Text modality encoder."""
    
    def __init__(self, model_name: str = "clip"):
        self.model = self.load_model(model_name)
        
    def encode(self, text: str) -> np.ndarray:
        """Encode text to embedding."""
        return self.model.encode_text(text)
    
    def decode(self, embedding: np.ndarray) -> str:
        """Decode embedding to text."""
        # Use generative decoding
        return self.model.generate_text(embedding)

class ImageEncoder(ModalityEncoder):
    """Image modality encoder."""
    
    def __init__(self, model_name: str = "clip"):
        self.model = self.load_model(model_name)
        
    def encode(self, image: np.ndarray) -> np.ndarray:
        """Encode image to embedding."""
        return self.model.encode_image(image)
    
    def decode(self, embedding: np.ndarray) -> np.ndarray:
        """Decode embedding to image."""
        # Use generative model
        return self.model.generate_image(embedding)

class AudioEncoder(ModalityEncoder):
    """Audio modality encoder."""
    
    def __init__(self):
        self.encoder = self.load_audio_encoder()
        self.decoder = self.load_audio_decoder()
        
    def encode(self, audio: np.ndarray) -> np.ndarray:
        """Encode audio to embedding."""
        return self.encoder.encode(audio)
    
    def decode(self, embedding: np.ndarray) -> np.ndarray:
        """Decode embedding to audio."""
        return self.decoder.generate(embedding)

class CodeEncoder(ModalityEncoder):
    """Code modality encoder."""
    
    def __init__(self):
        self.encoder = self.load_code_encoder()
        
    def encode(self, code: str) -> np.ndarray:
        """Encode code to embedding."""
        # Parse code structure
        ast = self.parse_code(code)
        
        # Encode structure and semantics
        structure_embedding = self.encode_structure(ast)
        semantic_embedding = self.encode_semantics(code)
        
        return np.concatenate([structure_embedding, semantic_embedding])
    
    def decode(self, embedding: np.ndarray) -> str:
        """Decode embedding to code."""
        return self.generate_code(embedding)
```

## Unified Semantic Space

```python
class UnifiedSemanticSpace:
    """Unified representation space for all modalities."""
    
    def __init__(self, dimension: int = 768):
        self.dimension = dimension
        self.alignment_matrices = {}
        self.modality_offsets = {}
        
    def align_modalities(self, source: str, target: str,
                         pairs: List[tuple]):
        """Learn alignment between modalities."""
        # Collect embeddings
        source_embeds = []
        target_embeds = []
        
        for src_content, tgt_content in pairs:
            src_embed = self.encode(source, src_content)
            tgt_embed = self.encode(target, tgt_content)
            source_embeds.append(src_embed)
            target_embeds.append(tgt_embed)
        
        # Learn alignment matrix
        alignment = self.learn_alignment(source_embeds, target_embeds)
        self.alignment_matrices[(source, target)] = alignment
        
    def translate(self, embedding: np.ndarray, 
                  source: str, target: str) -> np.ndarray:
        """Translate embedding between modalities."""
        # Get alignment matrix
        key = (source, target)
        if key in self.alignment_matrices:
            alignment = self.alignment_matrices[key]
        else:
            # Use indirect alignment
            alignment = self.find_indirect_alignment(source, target)
        
        # Apply alignment
        translated = np.dot(embedding, alignment)
        
        # Apply modality offset
        if target in self.modality_offsets:
            translated += self.modality_offsets[target]
        
        return translated
    
    def find_indirect_alignment(self, source: str, 
                                 target: str) -> np.ndarray:
        """Find alignment via intermediate modalities."""
        # Find path through alignment graph
        path = self.find_alignment_path(source, target)
        
        # Compose alignments
        alignment = np.eye(self.dimension)
        for i in range(len(path) - 1):
            key = (path[i], path[i+1])
            alignment = np.dot(alignment, self.alignment_matrices[key])
        
        return alignment
```

## Cross-Modal Translation

```python
class CrossModalTranslator:
    """Translate between modalities."""
    
    def __init__(self):
        self.encoders = {
            'text': TextEncoder(),
            'image': ImageEncoder(),
            'audio': AudioEncoder(),
            'code': CodeEncoder()
        }
        self.semantic_space = UnifiedSemanticSpace()
        
    def translate(self, content: Any, source: str, 
                  target: str) -> Any:
        """Translate content between modalities."""
        # Encode source
        encoder = self.encoders[source]
        embedding = encoder.encode(content)
        
        # Translate in semantic space
        translated_embedding = self.semantic_space.translate(
            embedding, source, target
        )
        
        # Decode target
        decoder = self.encoders[target]
        result = decoder.decode(translated_embedding)
        
        return result
    
    def text_to_image(self, text: str) -> np.ndarray:
        """Generate image from text description."""
        return self.translate(text, 'text', 'image')
    
    def image_to_text(self, image: np.ndarray) -> str:
        """Describe image in text."""
        return self.translate(image, 'image', 'text')
    
    def audio_to_text(self, audio: np.ndarray) -> str:
        """Transcribe and describe audio."""
        return self.translate(audio, 'audio', 'text')
    
    def text_to_audio(self, text: str) -> np.ndarray:
        """Generate audio from text."""
        return self.translate(text, 'text', 'audio')
    
    def code_to_text(self, code: str) -> str:
        """Explain code in natural language."""
        return self.translate(code, 'code', 'text')
    
    def text_to_code(self, description: str, 
                     language: str = 'python') -> str:
        """Generate code from description."""
        return self.translate(description, 'text', 'code')
```

## Multi-Modal Synthesis

```python
class MultiModalSynthesizer:
    """Synthesize content across multiple modalities."""
    
    def __init__(self):
        self.translator = CrossModalTranslator()
        
    def synthesize(self, inputs: Dict[str, Any], 
                   target: str) -> Any:
        """Synthesize from multiple modalities to target."""
        # Encode all inputs
        embeddings = []
        weights = []
        
        for modality, content in inputs.items():
            encoder = self.translator.encoders[modality]
            embedding = encoder.encode(content)
            embeddings.append(embedding)
            
            # Weight by modality importance
            weights.append(self.get_modality_weight(modality, target))
        
        # Combine embeddings
        combined = self.weighted_combine(embeddings, weights)
        
        # Decode to target
        decoder = self.translator.encoders[target]
        result = decoder.decode(combined)
        
        return result
    
    def create_multimodal_document(self, 
                                   content: Dict[str, Any]) -> Dict:
        """Create document with multiple modalities."""
        document = {
            'text': None,
            'images': [],
            'audio': None,
            'code': None
        }
        
        # Extract core semantic content
        semantic = self.extract_semantic_core(content)
        
        # Generate each modality
        if 'text' in content or 'audio' in content:
            document['text'] = self.generate_text(semantic)
        
        if 'image' in content or 'text' in content:
            document['images'] = self.generate_images(semantic)
        
        if 'audio' in content or 'text' in content:
            document['audio'] = self.generate_audio(semantic)
        
        if 'code' in content or 'text' in content:
            document['code'] = self.generate_code(semantic)
        
        return document
```

## Semantic Preservation

```python
class SemanticPreserver:
    """Ensure semantic meaning is preserved across modalities."""
    
    def __init__(self):
        self.semantic_tests = []
        
    def verify_preservation(self, source: Any, target: Any,
                            source_modality: str,
                            target_modality: str) -> float:
        """Verify semantic preservation."""
        # Extract semantic content
        source_semantic = self.extract_semantic(source, source_modality)
        target_semantic = self.extract_semantic(target, target_modality)
        
        # Compare semantic content
        similarity = self.compute_semantic_similarity(
            source_semantic, target_semantic
        )
        
        return similarity
    
    def extract_semantic(self, content: Any, 
                         modality: str) -> Dict:
        """Extract semantic content from modality."""
        if modality == 'text':
            return {
                'entities': self.extract_entities(content),
                'relations': self.extract_relations(content),
                'events': self.extract_events(content),
                'attributes': self.extract_attributes(content)
            }
        elif modality == 'image':
            return {
                'objects': self.detect_objects(content),
                'scene': self.classify_scene(content),
                'actions': self.detect_actions(content),
                'attributes': self.extract_visual_attributes(content)
            }
        elif modality == 'audio':
            return {
                'transcript': self.transcribe(content),
                'sounds': self.classify_sounds(content),
                'speakers': self.identify_speakers(content),
                'emotions': self.detect_emotions(content)
            }
        # ... other modalities
```

## Process

1. **Identify Modalities**: Determine source and target
2. **Encode Source**: Convert to unified embedding
3. **Translate**: Map to target modality space
4. **Decode Target**: Generate target content
5. **Verify**: Check semantic preservation
6. **Refine**: Iterate if needed

## Output

- Cross-modal translations
- Semantic preservation scores
- Multi-modal documents
- Unified embeddings
